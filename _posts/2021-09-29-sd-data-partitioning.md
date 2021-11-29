---
layout: post
title: "[System Design] Data Partitioning"
description: ""
comments: true
keywords: ""
---






Justification: after a certain scale point, cheaper and feaisble to scale **horizontally** rather than vertically

## 1. Partitioning Methods
#### a. Horizontal Partitioning(Data Sharding)
e.g. 
```
Table 1: ZIP code < 10000
Table 2: ZIP code > 10000
```

problems: 
1. hard to pick value range -> unbalanced server

#### b. Vertical Partitioning
e.g. 
```
Profile： Server1
Photo:   Server2
```
Pros: 
- easy to implement
Cons: 
- for user growth, needs to further parition for a specific server


#### c. Directory-Based Partitioning

Lookup service: to find out where a particular data entity resides,
This means the **GetDatabaseFor()** method actually
hits a web service or a database that stores/returns the mapping between each entity key and the database server it resides on.
: [Entity Key -> Database Server]

 This **loosely coupled approach** means you can perform tasks like adding servers to the database pool
or change your partitioning scheme without having to impact your application.


## Partitioning Criteria

a. Key or Hash-based Partitioning: 
problem: rehash is problematic, as the hash function may change when server added. 