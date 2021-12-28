---
layout: post
title: "[System Design] Push Pull Model"
description: ""
comments: true
keywords: ""
---


### Pros and Cons of push and pull

|           | push                                                                                                                                                                                                                                                                                                                                                                  | pull                                                                                                                                                                  |
|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|           | Most Common, rest/rpc, default                                                                                                                                                                                                                                                                                                                                        |                                                                                                                                                                       |
| mechanism | Client makes **push**<br> task to server to execute                                                                                                                                                                                                                                                                                                                   | Server request work from Client, <br>usually client has a task queue, <br>Server **pull** work from the task queue                                                    |
| adv       | 1. for request-response operations, <br>usually involves a persistent connection <br>over which both the request and the response travel<br> easy response matching, over the same connection<br>2. greedy routing, rather than blindly send to LB,<br>client can pick locally better server(low error rate, <br>fast response, distance closer, like same DC/region) | 1. message queue to distribute tasks, improve scalability, multiple worker(server) process at the same time<br>2. with message queue, no service discovery needed     |
| disadv    | 1. maintainance for list of server address<br>- LB endpoints<br>- DNS names, <br>- service discovery system(Zookeeper)<br>2. Load balancing?                                                                                                                                                                                                                          | 1. trouble to recv response: <br>- add http server to send address with msg<br>- broadcast response and client pick they want<br>2. don't support for complex routing |
|           |                                                                                                                                                                                                                                                                                                                                                                       |                                                                                                                                                                       |


### push model
![push graph](/assets/images/push_model.png)

### pull model
![](/assets/images/pull_model.png)


Service Mesh: 
the clients just need to know how to make a basic request, and the service mesh can handle 
- routing, 
- service discovery, 
- retries, 
- circuit breakers, 
- instrumentation, and more.


ref: 
https://medium.com/@_JeffPoole/thoughts-on-push-vs-pull-architectures-666f1eab20c2

