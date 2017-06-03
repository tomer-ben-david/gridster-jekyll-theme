---
layout: post
title:  "Hadoop CheaSheet"
date:   2017-04-22 22:18:00
categories: cheatsheet,hadoop,bigdata
comments: true
---
## Introduction

We have decided to aggregate in a single post the most important things to know about hadoop in a concise way.  Let's us know if you have any comments!

<iframe src="https://drive.google.com/file/d/0B3YbDgIxeEikMFF4MW83WWI4XzQ/preview" width="640" height="480" frameBorder="0"></iframe>

## Hadoop

```bash
##########
## HDFS ##
##########

NameNode # => Managing filesystem namespace, if you loose it you have no pointers to your data, you practially lost your data.

DataNode # => You know it holds data, installed on each worker.

Block # => Each file split to B1,B2,.. where each block size 128MB replication is on blocks.  Name node knows that File X is split to B1,B2 and where.

##########
## YARN ##
##########

ResourceManager # => Like `NameNode` for computing, tracks NodeManagers and how available they are for work.

NodeManager # => Like `Datanode` for computing, offer computational resources run applications tasks in containers.

ApplicationMaster # => Each application has `ApplicationMaster` process which negotiates resources with `ResourceManager` which delivers a `container` descriptor back to `ApplicationMaster` processa and asks `NodeManager` to launch the `container.`
 
 ################
 ## Map Reduce ##
 ################
 
 Map(k1, v1) --> list(k2, v2) # => map takes keyvalue pair and produces zero or more intermediate keyvalue pairs
 
 Recduce(k2, list(v2)) --> list(k3, v3) # => Reduce take a single key and list of values and produces zero or more keyvalue, usually aggregation.
```

## Summary

We kept that post small so you could rest :), but in general we went through what NameNode, DataNode, Block, ResourceManager, NodeManager, ApplicationMaster, Task are in a very short and concise way, isn't that just great :) If you liked it please hit the share button below and leave a comment for any comment! :)