1. The number of pages, reltuples, number of pages tagged in visibility map are in pg_class
2. These stats are updated when running auto vaccumming. For the stats a sample of data from the tables is sampled
3. Running ANALYZE updates the stats of the table
4. NULL values are shown in column level analysis represented as null_frac
5. Correlation field in pg_stats table denotes for a attrname what the order is close to. If its close to -1 its mostly DESC if its close to 1 its mostly ASC otherwise if its close to 0 its mostly random. It is used to compute the cost of index only scans.

#### Expression Statistics
To create statistics for expressions we need to create database objects out of it.