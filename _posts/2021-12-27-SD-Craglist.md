---
layout: post
title: "[System Design] Craglist"
description: ""
comments: true
keywords: ""
---


1. Bussiness Use Case
   Basic Functions: 
      1. ~~User Registration Function: email address/password/user_id~~ -> not need, simplify to send email to user with token,
      2. fill in your email address, email will sent to you: URL with token -> full control of the post. 
      3. Posting with image, -> CUD
      4. view the posts,   -> R
      5. Fill in email addr -> generate tokenL 256-512, like jwt token( -> Auth2)
      6. an email sent to you: URL -> full control over the post,
         1. 
2. Capacity Estimationa and Constraints, 
   zipcode: 46k -> 10 category
   each catogory -> 200 posts per day

   Peak QPS = QPS * 5/10



   QPS： 
   app server - stateless -> elastic adjusting
   - write 5k -》 1 server（一般one server 可以handle 10k） -> 1D for write DB server
   - read 5k * 100 -> (50 DB server(QPS:10k) for read request if no Cache)
   应该分开算，因为不同存储位置
   Text each 2KB  -> 1.4TB-》 
   Image/Video each 10KB -> Clodinary/S3

   client -> LB -> app server(stateless,elastoc) -> at least 1 DB_write
                                                 -> at least 500 DB_read 
                                              - > not SPF




3. Architecture of the whole system

一般response 不列出来，default to have



4. API Design

   createPost(~~userId~~, location, ~~createdTime~~, ~~expiredTime~~, content, title, imageUrls, contactInfo(phone,email) )
         * **we don't trust anything from Client**, like time. 
         * should not provide the creationTime from Client, 
         * we cannot allow the client to define the time, because it can be anytime defined by the client, 
         * client can create anytime, 
         * time should be generation by the server

   updatePost(userId, )


5. DB Schema

   fixed schema? how about urls?    
   Post related ->  MySQL
   image -> S3, CDN Cache(data close to user, low latency, 10times fast)

6. BottleNecks. 
   1. **Read QPS >> Write QPS**: 500k v.s. 5k, 
      a. read from cache, memcache/redis -> read QPS 25k,（**加了read Cache， readQPS可以减少90-95%**）
      b. Craglist: 
         pipeline store in the DB, 
         pipeline store the 
         they build a pipeline -> post -> static html -> CDN
         server generate HTML for each region every hour from server side, **1hour latency**, 
         how to handle update? 

         read req -> CDB -> serve the static html page
         write req -> database -> only write to DB

      c. deduplication, from client side? 
   
      d. data partition for each reagion? CDN





MySQL: 
   10k QPS for complicated Query
   for simple Query better

DynamoDB:
   infinitely horizontal scale, if you want to pay





JWT:
https://zhuanlan.zhihu.com/p/70275218

1. for: authorization 

Authentication: verify who someone is,  confirm identity
1. what you know: password, security question or one-time pin, grants user access to just one session or transaction
2. what you posses: mobile device or app, security token, digital ID card
3. what you are: biometric e.g. fingerprint, retinal scan, facial recognition
Authorization: verify what specific app/files/data a user has access to, verify the service

 Authentication Server: https://auth0.com/blog/what-is-an-authentication-server/

 To manage access control, an authorization server will issue access tokens to the client that lists what permissions the current user has. -> JWTs
 authorization process depends on authentication. Authorization cannot be granted unless the identity of the user is verified.

Encryption:
1. HTTPS TLS end-to-end encryption
2. password transmitted by HTTPS, and then hashed by server 



Feedback: 
1. structural: Bussiness Case -> threshold/limit/constraints -> how many DB? how many replicas? how many for read/write DB
2. -> Architecture, 
3. -> API
4. DB Schema
5. BottleNecks -> optimization, cache, pipeline? 

读写分离
QPS：5k-10k  有一个DB， keep utilization under 50%
1 SQL server 500GB， 
* 3 for replicas
master/slave DB, write to master, read from slave/backup/secondary DB


**two threshold** for scale
1. from Data Size: -> 3 SQL server, *3 for durability
2. from QPS: 500k read QPS -> 50 Server(each 10k)   
we take the max for these two threshold

API design -> rest, rpc, psudo code api不是重点






DB backup

如果没有很了解，不要太focus on details, suppose we have end-to-end encrption/Dynamo infinite scale service 

要顺着面试官的思考来，比如面试官说不用user id

TB -> 1K->10k  server


