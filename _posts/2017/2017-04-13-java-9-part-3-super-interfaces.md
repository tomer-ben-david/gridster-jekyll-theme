---
layout: post
title:  "Java 9 Part 3 - Super Interfaces"
date:   2017-04-13 22:18:00
categories: dev,java,functional-programming
comments: true
published: false
---

## Introduction

In scala traits can have implementation, why not have implementation `java`? well that was introduced in `java 8` so the next natural step in `java 9` is to have private logic in the interfaces. In part 2 of this series we introduced the `java9 REPL` in this part we are going to examine the new functionality introduced into `interfaces`.

This tutorial is the second in going easy step by step on `Java 9` new features. `Java 9` Has the following new features:
 
`Java 9` Introduces many new concepts such as `REPL`, `factory methods` for common data structures, and more.  Let's see now what `java 9` has to say about private methods.

Although we refer to the previous tutorial we really like to start from zero, and what zero means for us is going to the website where you can download `java 9` and install it we will do it in step 1 we don't like any incomplete tutorials.  

Our plan:

Step 1: Download java 9 from scratch and install

Step 2: Create a `java 8` style interface with default implementation

Step 3: Implement the interface and call it

Step 4: Enrich the interface with private method

Step 5: Run the class and see that it reuses the private interface method

## Step 1: Download java 9

Although we already covered downloading `java 9` we always want to start from ground `zero` we don't want to force you to go to another tutorial in order to get any step, so here is how to download it into your `macos`:

Goto: [https://jdk9.java.net/download/](https://jdk9.java.net/download/) and click on the `jdk` relevant to your favorite and used `OS`.

and after download just click it to install (if you are on `macos`) and verify you have it installed:

```bash
tomerb@tomerb-mac.local:~$ java --version
java 9-ea
Java(TM) SE Runtime Environment (build 9-ea+164)
Java HotSpot(TM) 64-Bit Server VM (build 9-ea+164, mixed mode)
```

And it's up and running.

## Step 2: Code an interface

We are going to use `jshell` in order to create an interface lets create it.

```java
jshell> tomerb@tomerb-mac.local:~/tmp/java9-modules$ jshell
|  Welcome to JShell -- Version 9-ea
|  For an introduction type: /help intro

jshell> interface MyInterface {
   ...> default int doubleThis(int i) {
   ...> return i * 2;
   ...> }
   ...> }
|  created interface MyInterface
```
So we have an interface called `MyInterface`, in the next step we are going to implement it.

## Step 3: Implement the interface and call it

So let's implement the interface and then call it we are not going to define any method in the `class` so the default implementation would come from the... you guessed it the default implementation in the `interface`.

```java
jshell> class MyClass implements MyInterface {
   ...> }
|  created class MyClass

jshell> new MyClass().doubleThis(2); // MyInterface just doubledThis!
$3 ==> 4
```

## Step 4: Let's add a private method to the interface

So we are going to define 3 methods

1. `helpDouble` - private will.. help doubling.
1. `doubleThis` - public uses the private helpDouble.
1. `doubleDoubleThis` - public uses the private helpDouble.

So `helpDouble` the private function is going to get reused, which is basically why we ned private methods.

```java
jshell> interface MyInterface {
   ...> private int helpDouble(int i) {
   ...> return i * 2;
   ...> }
   ...> default int doubleThis(int i) {
   ...> return helpDouble(i);
   ...> }
   ...> default int doubleDoubleThis(int i) {
   ...> return helpDouble(i) * helpDouble(i);
   ...> }
   ...> }
|  replaced interface MyInterface
```

## Step 5: Run the code and see that private is used

Next let's reinstantiate our class and call `doubleThis` and `doubleDoubleThis` which are reusing `helpDouble`

```java
jshell> new MyClass().doubleThis(2);
$5 ==> 4

jshell> new MyClass().doubleDoubleThis(2);
$6 ==> 16

jshell> new MyClass().helpDouble(2);
|  Error:
|  cannot find symbol
|    symbol:   method helpDouble(int)
|  new MyClass().helpDouble(2);
|  ^----------------------^
```

As you could see the `REPL` nicely replaced our definition of the interface and has doubled and doubleDoubled our 2 by reusing the private method defined int he interface `MyInterface.helpDouble`.

## Summary

After using `REPL` in the previous tutorial we went on to investigate `java 9` by checking out it's `private interface methods`.  We started again from ground zero.  We have download from scratch `java9` then ran `jshell` the new `REPL` provided by `java 9`, We then ran some some defined our first interface with implementation.  We then created a class with implements that interface but that's so `java 8`.  So we went on and defined a private method with implementation in the interface that has helped the public interface implementations! stay tuned for the next parts!