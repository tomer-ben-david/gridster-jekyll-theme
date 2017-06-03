---
layout: post
title:  "Java 9 Part 5 - Flow the new Reactive Streams"
date:   2017-04-13 22:18:00
categories: java,java9,java 9,resources, tutorials, try with resources, design patters, software design
comments: true
published: false
---

## Introduction

This is part 5 of the `java 9` series.  While in part 4 we covered `try with reosurces` which has improved in `java 9` we are going to cover another cool and new concept in `java 9` a concise interface to `reactive stream`, which contains the ability for `non-blocking back pressure`.  You see, reactive streams are all around us in all up to date languages and library.  The main challenge however in reactive streams is not the implementation but a concise interface.  That is the minimal set of interface with good naming to capture that and that is exactly what `java 9` does!

## The main components of java 9 reactice streams

The main components of java 9 reactice streams are:

1. Publisher
1. Subscriber
1. Subscription

Easy, the subscriber subscribes to the publisher on a subscription to receive the stream.

Fopr that we have multiple methods such as:

1. Publisher::subscribe
1. Subscriber::onNext
1. Subscriber::onSubscription
1. Subsription::request

The names are rather self explanatory which is great.

## Implementing Flow

By now you should call `reactive streams` --> `Flow`.  In order to implement such a stream you need to implement: Publisher, Subscriber, Subscription.

Create the publisher:

```java
SubmissionPublisher<Integer> publisher = new SubmissionPublisher<>();
```

Create the subscriber:

```java
public class MySubscriber implements Flow.Subscriber<String> {
    private Subscription subscription;
    
    @Override public void onSubscribe(Flow.Subscription subscription) { // implemnet }
    @Override public void onNext(Integer item) { // implemnet }
    @Override public void onError(Throable t) { // implemnet }
    @Override public void onComplete() { // implemnet }
}
```

So all we needed to do was to create a publisher, then create a subscriber, note that the `subscriber` should have a `subscription` memeber.  and then the Subscriber implements all the methods related to the flow api:

1. onSubscribe
1. onNext
1. onError
1. onComplete



## Summary

In part 5 of this series we have covered the try with resources.  In part 5 we have seen that the reactive streams api has become much better and is called `Flow` api.  This api is concise and includes 3 main components, publisher, subscriber and subscription with subscriber implementing the methods for receiving the stream and notifying on errors.