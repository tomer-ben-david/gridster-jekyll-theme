---
layout: post
title:  "Akka CheatSheet"
date:   2017-04-22 22:18:00
categories: cheatsheet,akka,reactive,dev
comments: true
---
## Introduction

Let's review the main concepts in `akka`

## Akka

### Step 1: Create ActorSystem

```java
final ActorSystem system = ActorSystem.create() // => this is how you start with akka, create an ActorSystem.
```
The `ActorSystem` maintains `ThreadPools` and is up until you shut it down! and is a factory for creating actors and managing their lifecycle.

### Step 2: Code an actor

```java
public class MyAkkaActor extends AbstractActor { // Our actor class.
    public Receive createReceive() {
        return receiveBuilder()
            .match(DoIt.class, this::onDoIt) // run onDoIt function on DoIt message.
            .match(StopDoingIt.class, this.onStopDoingIt)
            .build(); // Now we can have reference to the Receive.
    }
}
```

### Step 3: Instantiate top level topology actor

```java
final Actor myAkkaActor = system.actorOf(Props.create(MyAkkaActor.class), "myAkkaActor");
```

if your actor needs a child actor use the `ActorContext` to create it, this is how we build the actor hierarchy.

### Step 5: Code a message

You don't call `methods` on an actor your send it immutable `objects` - `messages`

```java
public static class DoIt {
    public final int howManyTimes;
    public DoIt(int howManyTimes) {
        this.howManyTimes = howManyTimes;
    }
}
public static class StopDoingIt {}
public static class FasterFaster {}
```

### Step 6: Call an actor with a message

Ordering of messages from same actor is preserved. different sernders can be interleaved.

```java
myAkkaActor.tell(new DoIt(2), ActorRef.noSender()) // noSender sender not important one way communication.
myAkkaActor.tell(new DoIt(1), getSelf()) // sent a message from another actor. can send back message.
```

### Step 7: Send a message back to calling actor

```java
getSender().tell(new DoIt(1), getSelf()) // from within an actor send back message
```

if no sender, for example message was not sent from an actor message would be sent to `deadLetters`.  `deadLetter` is a special `Actor`.

### Step 7: Create child actor

```java
getContext().actorOf(Props.create(MyAkkaActor.class)); // create child actor from within an actor.
getContext().getChildren()[0].tell(new DoThis(1), getSelf()); // tell first child actor to do it!
getContext().parent() // a child actor looking for his famous father actor.
```

### Step 8: Fault tolerance and self healing

By default parent actor restarts it's children in case of failure but you can change that.

```java
.match(Terminated.class, this::onChildTerminated) // parent actor listening to a bad child terminating.
```

## Summary

