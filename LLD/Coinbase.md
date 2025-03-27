1. OOPS and SOLID.



### Domain Round
- 1 coding question with multiple parts of 1 hour. I solved 3 parts with working code and passed all test cases.
- Communication is also judged.
- Part 1: You're given a list of values of object property.
    - Example: The object is a tree.
    - Length:[ Short, medium, tall]
    - Leaf colour: [Green, yellow, red]
    - Like this, you have multiple properties.
    - Now create a list of random objects by randomly selecting each property's value.
- Part 2: Generate unique objects in part 1.
- Part 3: Add a rarity option to the properties. For example, if the 'Tall' property is 'rare' then there should be only a few tall trees when you create random trees in part 2.
Interviewer presented a problem where there is a large dataset in backend and needs to be displayed on UI. What are the efficient ways to fetch the data. But the answer he is looking for is **Pagination**. There is a brief discussion on different methods and strategies that can be used in Pagination. Trade-offs between different methods and which method should be choosed in which use case.

After the discussion interviewer asked me to code Cursor Based pagination on sample dataset he have. Please be prepared to support a lot of features in your implementation. I was asked to implement limit, multiple filters and nextLink as part of my implementation.

: [https://www.geeksforgeeks.org/find-longest-path-directed-acyclic-graph/](https://www.geeksforgeeks.org/find-longest-path-directed-acyclic-graph/)

The question was related to iterators. You can find the different type of questions under the company tag. I thought I had done well in this round, but i didn't write code for edge case scenarios in the first go and earlier on I think I overcomplicated things that was enough for a reject.
 don't remember the exact question but it was related to given a set of features for NFT's for ex {"eyes": blue,green,yellow} , {"hair": black, brown, blue} . Combining this features to form unique NFT's. The further parts just built upon this with uniqueness constraints and having only limited NFT's with a given feature. Was straightforward and the round went well


This Q was on leetcode but not to the detail it transforms into later.

- We have N transactions with <id, size, fee,>.
- We are given a block size of 100, we want to fill it with transactions such that the fee is maximized.
- We do not want an optimal, but a scalable solution (Basically no NP knapsack DP).

I went around making a class of Transaction and another child which would encapsulate the data from Transaction and used it to solve the problem via priority queue using the ratio as guiding principle. I did call out that knapsack is the optimal solution but its not scalable.

	Now each transaction has a parent transaction. Child transaction cannot be done until parent transaction is mined.  
The problem talked of some SSPF or something which was basically incentivizing the trader to mine a low fee parent by giving it along with a very high fee child.  
I brain farted in creating the graph (should have been a HashMap[id] = Transaction and then make a graph between IDs) so I did the problem in an alternate way.

I proposed Toposort to respect the parent child constraint and asked if we are fine iterating the list of transactions each time to check if any new child could be put. Interviewer said its a good Q and we are focussing on completeness and gave a go ahead to my Toposort so I went ahead with that solution.

For part 3, we ran out of time, but it was asking to calculate the fee a child Cn should have to incentivize the trader to take P->C1->C2->...->Cn in block, for all Cn.

I discussed graph making and recursive DFS to calculate the value in such a way that the parent-child list has best value to be at top of priority queue.

Given N transactions with <id, timestamp, user, currency, ...>, write a code to filter out records by time range, userId and currency.  
On surface, it looks like an easy problem of iterating through the list, but there are a few gatchas you can fall into:

- Do we want to support multiple filters at a time (Yes!)
- Are the filters joined by AND or an OR (i.e. a record is filtered if any filter passes or all filters must pass).

The interviewer was very experienced. He was live reporting any coding error I do - syntax, logic etc. Since I usually catch errors in a dry run after coding, this seemed like a setback to me as the itnerviewer caught some nits and logical errors as I was typing. Try to focus on writing code which works in first try itself, dont let time pressure get to you.

Once this is done, we discuss real life scenarios where a DB may return such a list and I highlighted that Pagination is needed in all such scenarios in production since god forbid we return a billion records and throttle everything down. (Apparently the title of this problem was pagination. 0 points to me, should have noticed it earlier xD).

He asks how I would implement pagination and I went with how we can make m_latestIndex a member of my current Database class (which is performing filtering) but that works for this problem, if I think of multi threading scenarios, I need to think more. To this, he prompted that we can make the method return this and each thread can continue without any issue.

Overall, we also had multiple back and forth discussions on OOP, ideal design for the classes and it was a learning experience for me, although I knew it would be a no hire.

### Tech Execution Round
**Round 4 - Tech Execution Interview**  
_Level 1:_  
Write an alternate iterator for list of lists. Similar to ZigZag Iterator on leetcode but you can have not just 2 but 'n' lists within the list;  
[https://leetcode.com/problems/zigzag-iterator/description/](https://leetcode.com/problems/zigzag-iterator/description/)

Example :  
lists = [[0, 1, 2], [], [3, 4], [5]]  
output should be 0, 3, 5, 1, 4, 2

_Level 2:_  
Write a range iterator. Should support negative step as well.  
Example :  
start = 0  
end = 10  
step = 2  
output should be [0, 2, 4, 6, 8, 10]

_Level 3:_  
Write a basic list iterator  
list = [0, 1, 2, 3, 4, 5]  
output should be 0, 1, 2, 3, 4, 5

_Level 4:_  
Modify the class written in level1 to take list of iterator objects instead of lists and print the numbers in alternate fashion. The iterators list can contain both range iterator and list iterator.


- Part 1: Design and code an interleaving iterator. Given 2 arrays output the elements in interleaving order.  
    `array1 = [1,2,3] and array2 = [4,5,6] -> result = [1,4,2,5,3,6]`
- Part 2: Design a range-based iterator with a given start, end and step value.  
    `(1, 10, 2) -> [1,3,5,7,9]`
- Part 3: Design interleaving iterator with a list of iterators
- Part 4: Add a filter function to interleaving iterators


You will be given multiple lists and we need to iterate one element from each list. For instance

Input Lists : [[1,2,3],[4,5,6]]  
Output: [1,4,2,5,3,6]

We can use the jave iterator methods and code the solution. I used the iterator methods and used a queue to achieve this.

The follow-up for this is to write two custom iterators.

```sql
1. **Basic Iterator** which is used to iterate a given list.
2. **Range Based Iterator** where you will be given a start, end and step. This will also have basic methods like getNext() and hasNext() which should work similar to the normal iterator methods.
```

Now we need to use the above custom iterators to iterate the list. The input can be a mix of basic and range based iterators. For instance.

Input : [[1,2,3], [4,5,6], [1,6,1]] // [1,6,1] is input for range based iterator. 1 is the start, 6 is the end, 1 is the step

Output : [1,4,1,2,5,2,3,6,3,4,5,6]
#### Behavioral
1. Tell me about a time you took a decision while you were lacking the data to make a decision?
2. Tell me about a time you had to go through several steps in order to get a solution right for a problem you were solving?
3. Tell me about the most complex project you handled?
4. How did you achieve consensus with the other cross-functional groups?



### Experience Links
https://leetcode.com/discuss/post/5843528/coinbase-sde3-remoteindia-sep-24-offer-b-v572/
