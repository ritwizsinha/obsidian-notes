
### Partitioning by userId
This leads to hot spots in the cases where some users post a lot and some don't .This is generally solved by the randomization strategy where along with userId we also attach a number x where x <= limit where limit is set manually such that the userIds are distributed across partitions as well. The best way though is just partition randomly


### Inverted Indexes
Inverted indexes can be unigram or can be bigram as well. In search systems, we can create unigram, bigram and trigram indexes to support queries like A and B and C.


### Things Learnt
1. CDNs for media
2. Inverted indexes with bigrams.
3. Lambda Architecture
4. Creating a hot and cold index.
5. Hot + Cold Data for systems with huge amount of data and finding some analysis of what might be required at a time.