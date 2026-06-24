

First step in query processing after parsing is creating a relational algebraic representation of the query.  A simple query like 

```sql
select salary from employees where salary < 75000
```

Can be represented as

SELECT[salary < 75000]\(PROJECT[SALARY]\)
AND
PROJECT[salary]\(SELECT[salary < 75000]\)


Where PROJECT is the filter expression and SELECT is used for choosing a row. In PROJECT duplicates are always eliminated
CROSS is a cartesian product. If two tables have A and B number of rows then the cross product has A * B rows
Natural Join performs cross product but then performs a SELECT on attributes with the same name.  ⋈
Theta Join is basically natural join with a simple SELECT condition.
For this filter we can use different algorithms for example b tree index search or linear search.
A relational algebraic instruction is annotated with the algorithm to use and is called an evaluation primitive


#### Cost Evaluation for Evaluation Primitive

For identify the cost we use cost metrics about IO delay and CPU costs depending what are dominating. In a distributed database the dominating force would be network requests to other servers and on the same server it would be the usual costs of IO and CPU 
The cost model used byPostgreSQL (as of 2018) includes (i) a CPU cost per tuple, (ii) a CPU cost for processing each index entry (in addition to the I/O cost), and (iii) a CPU cost per operator or function (such as arithmetic operators, comparison operators, and related functions)

For IO we use number of blocks transferred from storage and the number of random I/O accesses
Ts = Time taken between when the I/O request is made to when the first byte of data is received
Tt = Time taken for transfer of one block
Block writes are typically about twice as expensive as reads on magnetic disks, since disk systems read sectors back after they are written to verify that the write was successful
In PostgreSQL the total memory available to a query, called the eﬀective cache size, is assumed by default to be 4 gigabytes, for the purpose of cost estimation; if a query has several operators that run concurrently, the available memory has to be divided amongst the operators

We don't try to minimize the number of disk accesses itself in a query optimization problem because some queries might be better run with more disk accesses parallely. Thus we isntead try get the minimum resource consumption and that resource is seek and transfer time.

##### Operation Costs
Clustering index (also referred to as a primary index) is an index that allows the records of a file to be read in an order that corresponds to the physical order in the file. An index that is not a clustering index is called a secondary index or a nonclustering index.

Having equality conditions being imposed on secondary and primary clustered index have different implications, as if the secondary index values are not unique and lead to multiple matches we might have the situation where we are querying multiple blocks leading to extra seeks and transfers.

For comparison conditions like < index scans are not required since we can just linear scan and get the values but we need the index scans for >= queries

##### Bitmap Index Scan
Clever technique where we initialize a bitmap for all of the blocks in the table and then mark those as 1 where the secondary index returns a block. After that we run a linear scan and scan only those blocks which are enabled, this reduces transfer time but increases seek time.



##### Joins
Nested Loop Join
Two nested loops iterating the blocks. 
If one of the relations fits entirely in main memory, it is beneficial to use that
relation as the inner relation, since the inner relation would then be read only once.

Block Nested Loop Join
Similar to above, but here we iterate over every block in both relations rather than every tuple. This gives us br*bs + br number of block transfers in the worst case where we could have 2 blocks in memory. Total seeks would be 2* * br thus smaller relation should be outside.

Index Nested Loop Join
Instead of iterating through the inner loop we use the index to find tuples which match the key.
If index is present on both relations it is more effecient to keep the one with smaller number of blocks as teh outer relation. Seeks are considerably more expensive than block transfer


Merge Join
The common attributes between two tables are used as a basis for sorting on the table. Then we employ the merge step in merge sort to get the join result

Hybrid Merge Join

If one relation is sorted, another is unsorted but has a secondary index on the common attribute. We create an intermediary stage where we have a relation of sorted with pointers to the unsorted, we then sort the unsorted pointers giving linear access and then retrieve data from the unsorted block

Hash Join
We choose a hash function to create groups of tuple for each of the two relation. Then we only iterate in the common buckets as for all other buckets the attributes are not same. Within this bucket we do a Nested Index Loop Join per tuple. Hash joins can be used to join large sets of data, because if doesn't have enough memory it would spill to disk and then after it is done with some tuples which were in memory, it would start joining the ones in disk after loading them to memory


##### Aggregation

The cost of aggregation is same as cost of deduplication, we first sort and then aggregate in groups. If we have a group_by then we need to sort to find the tuples belonging to the same group.
**Aggregation needs grouping, not sorting. Sorting is just one way to achieve grouping. Hashing is the other common way.** If the number of groups is small enough to fit in memory, you can aggregate on the fly and avoid most of the extra I/O.


#### Evaluating Expressions

If in an operator tree we evaluate relations one at a time and materialize them then there would be the issue of writing this temp data to disk. Another method is to run separate operators in a pipeline, where each new tuple processed passes forward in the pipeline, meaning no need to store the data. 
In the operator tree the inputs to the leaf operators are relations. The main problem with materialized execution is writing all the temporary evaluations to disk
Double buffering allows us in this case to continue running the algorithm while the other buffer flushes itself to disk. 

To prevent this disk write we use pipelined execution where across non aggregatory operators we can pass on the evaluated expressions in up in the tree as soon as they are present.

Demand Driven Pipeline: The operators at the top periodically ask for tuples which pings the operators down below eventually cascading downwards. This is implemented as an iterator pattern in the operators

Producer Driven Pipeline: Generate tuples eagerly to pass forward. To implement this each operator would have a buffer for input and output, their aim would be to fill the output buffer if they have some entries in the input buffer and wait if the output buffer is full.

There are two kinds of edges in operator graphs
1. Pipelined (Non blocking) edges
2. Materialized edges, all operators connected to pipelined edges move concurrently

The operator tree can be divided into subtrees where the operators are pipelined and the edges between them are materialized. Each subtree is called a pipeline stage. The query processor executes the plan one pipeline stage at a time, and concurrently executes all the operators in a
single pipeline stage.
Operators like sort are not inherently blocking because the generation step of sort of creating a buffer and sorting it can consume instantly and the merge step can produce instantly for the consumer of merge. Thus the blocking step happens between generating all entries and merging them. Thus we can separate them into 2 iterators.

Indexed Nested Loop Join is pipelined on the outer loop because it can generate tuples as it gets tuples for the output relation but it is blocking on the inner loop because a index needs to be created first for all the values.

When a materialized edge is between suboperators of a single operator, for example between run generation and merge, the materialization cost has already been accounted for in the operators cost, and should not be added again