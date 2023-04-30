---
layout: post
title: "[System Design] Concepts"
description: ""
comments: true
keywords: ""
---

TODO:
1. SOAP b.s. REST v.s. RPC
2. user quota: You can use user quotas to limit the amount of disk space or the number of files that a user can use. like in linux, quotecheck  on a filesystem to limit user/group
3. cloud data storage service:
>  a. s3 over HDFS; 
> 1. Simply put, S3 is **elastic**, HDFS is not. 
> 2. **SLA** S3’s availability and durability is far superior to HDFS’.
> 3. **data integrity**, big data system with HDFS's **atomic rename feature** support atomic writes: output of a job is observed by the readers inan "all of nothing" fashion. S3 don't have data integrity support, -> requiring complicated application logic to guarantee data integrity
> -- <cite> https://databricks.com/blog/2017/05/31/top-5-reasons-for-choosing-s3-over-hdfs.html</cite>


4. on-premise sw
5. **Off the shelf software** are standardised software applications that are mass-produced, available to the general public, and fit for immediate use. They are designed for a broad range of customers, offering a comprehensive set of features to streamline operations.

6. Zookeeper:

7. Minimum viable product(MVP)
8. Know one consensus algorithm. 
9. etcd: etcd cannot be stored in memory(ram) they can only be persisted in disk storage, whereas redis can be cached in ram and can also be persisted in disk. etcd does not have various data types. It is made to store only kubernetes objects. But redis and other key-value stores have data-type flexibility.

9. battle-tested:redis, 
10. JWT:
    1. signature method: HS256 (HMAC with SHA-256), RS256
    2. SingleFactorAuthentication(UserName+Password)
    3. MultiFactorAuthentication(UP + OTP(One-Time Password))

11. Authentication Server
$$
12. interview能怎么简单怎么来，但是工作中，要前瞻性

   10mins: Bussiness(UseCases)/Common Sense
   10mins: Constraints
   10mins: Architecture
   10mins: API design
   10mins: DB Choice
   10mins: Data Model


13. disagree with design:
    1.  fundmentally not work, from theory not work
    2.  I don't know what's the best solution, but I don't think this is the best.


14. logical partition: physical partition: 
   more traffic server can redirect a req to spare server

15. reverse proxy: A reverse proxy server is a type of proxy server that typically sits **behind the firewall** in a private network and directs client requests to the appropriate backend server. A reverse proxy provides an additional level of abstraction and control to ensure the smooth flow of network traffic between clients and servers.


The difference between a forward and reverse proxy is subtle but important. A simplified way to sum it up would be to say that a forward proxy sits in front of a client and ensures that no origin server ever communicates directly with that specific client. On the other hand, a reverse proxy sits in front of an origin server and ensures that no client ever communicates directly with that origin server.

##  proxy:
### Forward Proxy
1. **collapsed forwarding**: forward proxy: combine the same req into one
2. can **hide** the identity of the client from the server by sending requests on behalf of the client.
3. used to cache data, filter requests, log requests, or transform requests (by adding/removing headers, encrypting/decrypting, or compressing a resource).
### reverse proxy
1.  can be used for caching, load balancing, anonymizing the servers, or routing requests to the appropriate servers.
2.  




### Pagination
but the parameter nextPageToken will allow you to page through multiple rows.





### Server capacity estimation


server: 1000 QPS -> more than 1 server, needs partitions, 
1k - 10k QPS:  1M(read-only)

DB server: Storage: 1 MySQL Server: 100GB 
Assuming a high-end server has 144GB of memory,
Btree level3: 50GB, 
      level4: TB


### UUID discussion
e.g. twitterId
1. encode creation time in ID: 
   a. bits for epoch time
   b. bits for the auto-increasing seq num: 
      We can reset our auto incrementing sequence every second. For **fault tolerance**and better performance, we can have **two database** servers to generate auto-incrementing keys for us, one generating **even numbered keys** and the other generating **odd numbered keys.**

   50 years: -> 5 * 86400 * 365 -> 1.6B ->   



# System Optimization:
https://sre.google/sre-book/addressing-cascading-failures/

### Cache:
   1. Slow Startup by Cold Caching

      In steady-state operation with a warm cache, only a few cache misses occur, but when the cache is completely empty, 100% of requests are costly. 
      mploy caches to keep a user’s state in RAM.

   2.  It’s a good idea to ensure that all clusters carry nominal load and that the caches are kept warm.

#### cache invalidation: 
###### write through: 
write to cache and DB simultaneously
pros: 1. data consistency. 2. nothing lost upon crash
cons: higher latency, double write

###### write-around: 
write to DB, bypass the cache; 
for the pattern, only write, but not re-read


###### write-back:
data to cache alone, completion is confirmed to clinet immediately
write back understand an interval
pros: for low-latency and high-throughput write-intensive app
cons: data loss of crash






