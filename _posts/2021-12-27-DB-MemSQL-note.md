---
layout: post
title: "[Database] DB Transaction"
description: ""
comments: true
keywords: ""
---

relational DB: SQL interface
in-memory: fast, data in mem first then -> disk
distributed: scale out, multiple hosts

nodes type: 
1. aggregator node: only metadata(data about data)
2. leaf node: data in here

1 master aggregator, 1 or more child aggregator

cluster
**MA** A 
**L** L L 


each node has SQL as interface

user query -> A -> fan out -> leaf node


DDL(data definition language) -> MA
   e.g. create + s/i/d/u 
DML(data manipulation language) -> 
   e,g, s/i/d/u

memSQl ops on MA:
1. UI
2. Cli

-> MemSQL

