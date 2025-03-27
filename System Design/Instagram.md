#### Functional Requirements
- Photo sharing app featuring videos, caption and images
- Users follow other users
- Each user has a personalized feed for the number of people they follow.
- Nested Comments

### Scale
- 500 M Dau
- 100M post per day.

#### Properties

#### Non Functional Requirements(CCS FLEDSD)
- Availability >> Consistency
- Low latency in fetching images/videos
- Eventual consistency in feeds.



#### Calculations
	 100M Posts per day
	 100,000,000 / 100000 = 1000 Posts/Second.
	  


### Learnings
- Feed Generation Using CDC. 
- Controlling the eventual consistency of feed generation. For each post we go through all the followers of the creator of the post and add a record for them, this can be problematic in the worker since for popular stars it might have to create 500M rows at once. Thus we might need to distinguish between between popular users, and this technique might only help with people who are not popular. Thus the 'celebrity problem' is not solved here. Thus for users with more than a certain number of accounts, we can create the feed for particular users after taking a look at the verified accounts they follow, getting the latest posts and adding them to the feed. Hybrid approach combining these two methods write amplification for non-verified accounts and read amplification for popular accounts, specially through caches.
- Here feeds are something which can be cached without knowing the access patterns, we can cache the postIds and then serve those postIds to the user.
- Hydrating posts is an issue as well, so apart from storing the postIds, we also store post metadata and have a TTL for the post metadata
	- 500M DAU. 1000 followers. 100M Posts. 1 Post Metadata(400 bytes) = 4,00 *  100,000,000 = 40000 Mb = 40 GB. Can be stored in cache.
- In youtube subscriber button, a bunch of people cannot be notified at the same time, thus there is priority to loyal followers and then all the other followers are notified after a while, check if we can use the same sort of feed generation logic for popular verified users.
- Single leader replication for consistency issues in comments loading.
- Show whether we need BFS or DFS for implementing comments


#### Things to Learn
- PreSigned URLS
- Upload Failures and Network interruptions in mutlipart upload in S3.
- CDNs
- ZSET
- Max Payload Size in HTTP < 2 GB
- Take care of resolution and devices, different devices don't need the same quality
- CDN Dynamic Media Optimization
- Move data to lesser expensive storages if not accessed frequently
- CDC on Flink for computing the feed, with in memory map join
- Flink 
- Joins in Stream Processing.