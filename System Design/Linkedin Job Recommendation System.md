![[Pasted image 20250306190933.png]]


### Learnings
1. Question says to pre filter based on exact specifics like skills and then move on to recommendations.
2. How do we stop one job showing again and again to a person (Frequency capping)

##### Frequency Capping
Frequency capping in a distributed system ensures that a user or entity is not shown a particular advertisement, notification, or content more than a specified number of times within a given period. Since distributed systems operate across multiple nodes.


#### General Recommendation System Tips
1. Push data to a database, 
2. CDC to Kafka
3. Run a stateful consumer like Flink to create recommendations for users and store them in Hadoop, S3 or Parquet files.
4. Then we run a batch job to update the cache based on the recommendations created.
##### Problems
1. If I go through my cache, and consume all the data I don't want to wait the next day to look at new recommendations.
2. We need interday refreshes

##### Embeddings.
Similarity recommendations, this is a vector representation of our entity

##### Recommendations are 2 step process.
	1. Retrieval: Fetch last x entities user interacted with, fetch last y most similar entities from this
	2. Ranking: Assign a score to all x * y embeddings and filter out entities seen before, sort them by score and return to user.


##### Partitioning Recommendation Servers
	It is beneficial to stick a user recommendation to a single server because of data locality reasons
	We can precache last x entities interacted with along with type of interaction.
	For lowering user distribution among distribution servers and promote sticky users we can use consistent hashing 


#### Fetching Similar Entities
1. Use a vector database, just like geospatial index, cache is necessary.
2. Using a cache which for a particular id stores k nearest entities to that id, when a new entity is added to the cache, how do we update the old ones.
   Example in cache we have something like
   12: 13, 66, 112
   34: 45, 57, 100
   when 18 comes in and is close to 12 how do we update the list for 12 ? We keep a min heap for each entity, sorted set and whenever a new entity is added we check for 18 what are its closest neighbours and update those distances if they need updating.


#### Solving Hot Partitions
1. Adding read only replicas, using caching
2. Use CQRS, the write was more effecient when sorted by some key but reads are more effecient when sorted by some other key. We use CDC and Kafka and partition by key2 and create a derived data store.


#### Assigning User Specific Rankings
Store some scaling factor for particular attributes on cache for a user and apply to the recommendations when sorting.



#### Filtering out sorted entities
Use Bloom Filters. It will answer you which entities have not been shown before with certainty. Though it would skip some entries which have not been seen before and never show it


#### Fault Tolerance with caching these things in Memory
Store the bloom filter in S3 along with Kafka log.