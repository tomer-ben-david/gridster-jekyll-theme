---
layout: post
title:  "Apache Storm CheatSheet"
date:   2017-04-22 22:18:00
categories: cheatsheet,storm,reactive,dev
comments: true
---
## Introduction

## Step1: Comparison to hadoop

1. In `hadoop` jobs finish (usually and hopefully :).  In `storm` they continously ingest events.
1. In hadoop you have `jobtracker` In `storm` you have `nimbus` assigning tasks to machines monitoring failures etc.
1. Each nodes runs `supervisor`, it listens to work and starts and stops `workers` to do some job based on what `nimbus` assigned to it.  `workers` runs subset of topology could run multiple `bolts` for example.
1. `zookeeper` coordinates `nimbus` and `supervisors`.
1. `nimbus` and `supervistors` are stateless you can kill nimbus or supervistor and they will come back like nothing has happened.

## Step 2: Topologies

A `graph` of computation.  To run a topology:

1. package all your code in a jar
1. run the jar `storm jar mycode.jar MyMainTopology arg1 arg2`
1. You can use any programming language! as all communication and input and output are in `thrift`.

## Step 3: Spouts and bolts

1. spouts read stream, for example they register to some stream.
1. bolts do omputations, and can emit streams or multiple streams talk to `database` etc.
1. When `bolt` emit stream it would be received by all `bolts` that registered to that `stream`
1. You can store data stateful in your bolt that is less recommanded but you can do that. if restarted memory is restated.
1. when you store something in memory better use `fieldgrouping` so the same `keys` would reach the same `bolts`.  in shutflegrouping the spread is random.

## Step 4: parallelism

1. You can define how much parallelism you want for each node and storm will spawn that number of threads across the cluste rlke parallelism for a specific `bolt`.


