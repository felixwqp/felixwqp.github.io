---
layout: post
title: "[System Design] Transaction"
description: ""
comments: true
keywords: ""
---

## Transaction:
old: order whole process; 
new: crud 

SQL -> transaction

something always go wrong:
- DB SW/HW fail at anytime
- app may crash at anytime
- Network partition can cut off app from db, or one db node from others
- several clients may write to the same DB, overwriting other's change.
- Partial update, half json, 
- race condition

for any two transactions, there are infinite tiny-transactions happening within it, they may fail. how to handle concurrency control


#### Transaction: a neat abstraction
def: a way for an app to group several reads and writes together into a logical unit. they are in an atomic section:
- success: commit 
- fail: abort, rollback
easy to handle error, because no partial failure.


NoSQL give up strong transaction; efficiency > safety
Transaction:  -> safety, auti-thesis for large scale system

#### ACID:(1983) v.s.  BASE
- Atomicity: All or nothing: if trans aborted, app know nothing change, -> safely retry
- Consistency: it's not guarantee of DB, it's app'duty, application's notion of invariants must be guaranteed. 
- Isolation: concurrently executing trans are isolated to each other, must be serially. rather than 
- Durability: if data committed: -> check point; by write-ahead log, before check point, nothing can guaranteed,(HW fault, DB crash); after check point written to non-volatile storage such as hard drive, SSD


#### Single Obj  v.s. Multi Obj
- multi-obj transaction: some way of determining which read/write operations belong to the same transaction. in SQL, connection based on TCP, a trans may last forever

- in no sql, no way of grouping operations together
(nosql has the multi-obj API, )


##### Transform multi-obj to single obj
- in multi-obj trans, allow to ensure the reference(like foreign key) is valid, 
hard to figure out the **dangling pointer** and **cascade delete**
- in document data model, vals need to be updated together usually within the same document, single object; -> transaction keeps denormalized data sync update
- in DB with secondary indexes; -> updated in one idx, not the other.

##### Read Uncommitted
```SQL
SELECT COUNT(*) FROM emails WHERE recipient_id = 2 AND unread_flag = true
```

###### isolation level
- dirty read:
- dirty write: only overwrite committed data, cannot overwrite uncommitted data

- read committed:  

no: dynammo
- Snapshot Isolation
impl, read from seq before the commit number

- read modify write
-> atomic
1. detect lost update and abort/retry or force read-modify-write cycles sequentially 
2. CAS
3. Conflict resolution and replication

[What Is Idempotence?](https://www.restapitutorial.com/lessons/idempotency.html)
###### consistency control

#### Read Committed
###### multi-object atomicity
###### Row-level locking 
###### no dirty writes/reads
### Snapshot isolation
###### MVCC
###### No Read Skew
###### No Lost Updates



### Serializable
###### 


### consistency

#### CAP

#### ACID


### snapshot isolation
 

e.g.
1. calandar
 如何用timing 举反例， break consistency
2. payments:
-  every step may fail
-  
```
- felix -> A store
- balance of felix -= 500
- balance A store += 500
```

Common problem:
1. calendar room booking conflict
2. unique id: 


Optimistic Locking v.s. Pessimistic locking:
https://stackoverflow.com/questions/129329/optimistic-vs-pessimistic-locking

DB locking from DB system! 

write skew problem: for efficiency DB locking
1. two phase locking: read/write blocked
2. meterial conflict: write/read non blocking
3. lock on index
   1. lock on room
   2. lock on timeslot




In memory: req 1 -> Room A {locked}
then server crashed: A release

Room A[lock] -> DB crash 



read / write not blocking





