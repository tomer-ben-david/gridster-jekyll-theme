---
layout: post
title:  "Kafka CheatSheet"
date:   2017-04-22 22:18:00
categories: cheatsheet,kafka,reactive,dev
comments: true
---
## Introduction

Let's review the main concepts in `kafka`

## Step 1: Choose your seriealization

Choose whichever serialization method you want, if you want to serialize multiple message in one sending do it.

## Step 2: Scale

You can publish and consume for the same `topic` on multiple brokers.

topic1/partition1 (can be on broker1 within this you maintani order for consumers meaning first in will be first out for consumer)
topic1/partition2 (can be on broker2)



## Step 3: Storage

1. Each partition corresponds to logical log.
1. Messages do not have explicit ids, they have logical offset, reduces complexity, no random seek.
1. Each consumer pull request ocntains the offset to consume from.

## Step 4: Kafka Broker

Kafka brokers are `stateless` this is not the case in other messaging queues!  The consumer holds it's sequence!  How does the broker knows when to delete messages? => SLA, retention. if message is on broker longer than a period.

Consumer can violate queue and rewind and reread messages.

## Step 5: Zookeper

Agreeing which serer is alive network failures etc.  Each kafka broker coordinates with other brokers via zookeper.  

## Step 6: Kafka vs others

1. 5 to 10 times faster.
1. Producer doesnt wait for acknoledgment.
1. Can batch multiple messages in one send receive.
1. More efficient storage format.
1. Consumer hodls the sequence id thus kafka is stateless.
1. 

## Summary

