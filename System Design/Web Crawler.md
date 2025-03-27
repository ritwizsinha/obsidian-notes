### Some topics to learn about
1. What is the complexity for regex matches. 
   For DFA(Deterministic Finite Automata) the complexity is O(N) otherwise it becomes exponential.
2. Does each domain name have its unique robots.txt
   Yes
3. Do crawlers crawl a page mentioned in the robots.txt as disallowed if it is coming from a different website.
   No good crawlers cache the robots.txt and check the cache if they encounter some URI.
4. Given a particular domain, can we get all of the URIs that are possible in this domain?
   No, but we can discover many using:

	1. **`robots.txt`** – Hints at blocked paths.
	2. **`sitemap.xml`** – Lists important pages.
	3. **Web Crawlers** – Finds linked pages.
	4. **Google `site:` Search** – Shows indexed pages.
	5. **Wayback Machine** – Reveals old/deleted pages.
	6. **OSINT Tools (`gobuster`, `ffuf`)** – Brute-force hidden paths.

🔹 **No method finds all URIs, but combining them helps.** 🚀


#### How does robots.txt prevent overwhelming the system?
❌ No, it only **guides** well-behaved bots but **cannot stop malicious bots**.

### 🔹 **How It Helps:**

- **Limits crawling scope** (reduces load).
- **Can suggest `Crawl-Delay`** (some bots follow it).

### 🔥 **Why It Fails:**

- **Malicious bots ignore it.**
- **No enforcement or IP blocking.**
- **Bots can still overload the server.**

### ✅ **How to Actually Prevent DDoS:**

1. **Rate limiting** (limit requests per IP).
2. **Bot detection & CAPTCHAs** (e.g., Cloudflare, reCAPTCHA).
3. **Block bad user-agents/IPs** (`fail2ban`, firewall).
4. **Monitor logs** for unusual traffic.

🚀 **Use server-side protections; `robots.txt` alone is not enough.**



### Kafka with Exponential Backoff.
Causes head of line blocking in the particular partition. The standard solution is to move it to a separate retry topic which is evaluated at a slower pace with exponential backoff. If it causes issues even then, it is moved to a deadletter queue.


### Politeness for websites.
Politeness needs to ensure that we are rate limited per domain. So we can have a TTL for a domain in redis for 1 second or whatever time is specified in the CrawlDelay section of robots.txt and if another URI with that domain is encountered we move it to the back of the queue.


### What do we do for pages that have different URL's but same content?
Hash and chunk and cache the hashes.


### What to do for caching DNS lookups, handling failures and not overwhelming the DNS servers.
DNS is one potential bottleneck that is often overlooked. If we're using a 3rd party DNS provider, we'll want to make sure they can handle the load. Most 3rd party providers have rate limits that can be increased by throwing money at them. While this is certainly an option, especially given our time constraints, it's worth considering other optimizations:

1. **DNS caching**: We can cache DNS lookups in our crawlers to reduce the number of DNS requests we need to make. This way all URLs to the same domain will reuse the same DNS lookup.
    
2. **Multiple DNS providers**: We can use multiple DNS providers and round-robin between them. This can help distribute the load across multiple providers and reduce the risk of hitting rate limits.
Another idea for partitioning could be on the basis of domain hashing, this would direct similar domains to the same nodes which would already have a cache for the DNS entry. Although this might mean that some nodes are very busy while some are idle.


#### For Data Processing Questions
If you get a data processing question like this, your first thought should be to break the system down into smaller, pipelined stages. Pipelining allows you to isolate failures to a single stage and retry that stage without losing progress on the rest of the data. It also allows us to scale each stage independently and optimize each stage for its specific task.



### Rate Limiting the requests 
We also need to respect the rate limit of 1 domain a second. With multiple crawlers, this can get a little trickier since, in theory, all N crawlers could be hitting a single domain at the same time.

We can implement a global, domain-specific rate limiting mechanism using a centralized data store (like Redis) to track request counts per domain per second. Each crawler, before making a request, checks this store to ensure the rate limit has not been exceeded. We'll use a sliding window algorithm to track the number of requests per domain per second. If the rate limit has been exceeded, the crawler will wait until the next second to make the request.
A potential issue with this method is the risk of synchronized behavior among multiple crawlers. If several crawlers are waiting to make requests and simultaneously retry when the rate limit window resets, they'll all try and only one will succeed and the process will repeat.

Fortunately, there is a relatively simple solution to this problem: jitter. By introducing a small amount of randomness to the rate-limiting algorithm, we can prevent synchronized behavior among crawlers. This jitter can be implemented by adding a random delay to each crawler's request, ensuring that they do not all retry at the same time.



### Ensuring that 10 B URL's are parsed in 5 days.

This is an IO intensive task.
Each website data: 2 Mb
AWS IO optimized instance: 400 Gbps
Number of pages = 400 / 8 / 2Mb = 25,000 pages.
Let's say we use 40 % of this = 10k/second

Thus for 10B pages = 10 * 10^10 / 10 * 10^3 = 10^7 = 100 Days.
Ie we need 20 machines.


### Handling crawler traps
To handle crawler traps which tries to keep crawlers on the site forever, we can have something like a depth variable and break out if the depth variable goes out of certain bounds.



### Url Scheduler
For popular websites, we might need to update the data continously and parse it, for doing this we can schedule a job for the website in the future and push it to the queue.


### Problems with FIFO Queue in exploration
One  problem with storing in FIFO manner is that one domain subtree is completely explored and put in the queue leading to repeated requests to the same domain, one way to get over that might be to have a priority queue instead of a normal queue and reduce the priority of this links.


### Front and back Queues Concept
In crawling the web we need to ensure both politeness and priority. This can be done using a multilevel queueing scheme. We have a Front Queue which selects items with increasing probability based on their probability(Can be done with an array and binary search, like the Number Picker Problem). After sorting here we go to the back queues, these back queues contain different queues where each queue contains a singular domain, this is then distributed to worker threads.


### Consistent hashing
Consistent hashing can be used with downloader servers.