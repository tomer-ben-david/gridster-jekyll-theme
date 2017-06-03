---
layout: post
title:  "Java 9 Part 2 - JShell"
date:   2017-04-13 22:18:00
categories: dev,java,functional-programming
comments: true
published: false
---

## Introduction

Any up to date programming language has a `REPL` in todays world where you have `2 week` spring and you need to learn while coding, its important to have a fast feedback loop.  You don't want to setup a whole project just to test something in code right? The `REPL` brings you just this.  `REPL` are common in `scala`, `python`, `ruby`, `shells scripts`, and now also in ... `java`!

This tutorial is the second in going easy step by step on `Java 9` new features. `Java 9` Has the following new features:
 
1. Java 9 REPL (`JShell`)
1. Factory Methods for `Immutable List`, `Set`, `Map` and `Map.Entry`.
1. Private methods in Interfaces.
1. Java 9 Module System.
1. Process API Improvements.
1. Try With Resources Improvement.
1. CompletableFuture API Improvements.
1. Reactive Streams.

In this tutorial we are going to go from zero into a working `jshell`.  

Our plan:

Step 1: Download java 9 from scratch and install

Step 2: Run the shell

Step 3: Get Help from java 9 REPL

Step 4: Run a few calculations in jshell.

Step 5: Define a function and use it.

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

## Step 2: Run JShell

Running `jshell` is just.. typing `jshell` in your `terminal` lets try it:

```bash
$ jshell
|  Welcome to JShell -- Version 9-ea
|  For an introduction type: /help intro

jshell> 
```

cool.

## Step 3: Get Help

Type `/help` to get list of things you can do with `jshell`

```bash
jshell> /help
|  Type a Java language expression, statement, or declaration.
|  Or type one of the following commands:
|  /list [<name or id>|-all|-start]
|  	list the source you have typed
|  /edit <name or id>
|  	edit a source entry referenced by name or id
|  /drop <name or id>
|  	delete a source entry referenced by name or id
|  /save [-all|-history|-start] <file>
|  	Save snippet source to a file.
|  /open <file>
|  	open a file as source input
|  /vars [<name or id>|-all|-start]
|  	list the declared variables and their values
|  /methods [<name or id>|-all|-start]
|  	list the declared methods and their signatures
|  /types [<name or id>|-all|-start]
|  	list the declared types
|  /imports 
|  	list the imported items
```

One of the interesting commands above is `/imports`, lets see if we have ones by default:

```java
jshell> /imports
|    import java.io.*
|    import java.math.*
|    import java.net.*
|    import java.nio.file.*
|    import java.util.*
|    import java.util.concurrent.*
|    import java.util.function.*
|    import java.util.prefs.*
|    import java.util.regex.*
|    import java.util.stream.*
```

Yes we do, some `regexp`, `math`, `io`. cool.

## Step 4: Basic calcs

Let's try out some basic calculations and see how our shell works as a nice little `calculator` (you can uninstall the one you have)

```java
jshell> 1+1
$1 ==> 2

jshell> 5%7
$2 ==> 5

jshell> 13%3
$3 ==> 1

jshell> $2
$2 ==> 5

jshell> $2 // we got variable number two!
$2 ==> 5
```

so every result was placed in a numeric var starting with `$`.

## Step 5: Let's code a function

lets define a small function that takes an int and doubles it.  Then lets call it.

```java
jshell> int doubleThis(int i) { return i*2; }
|  created method doubleThis(int)

jshell> doubleThis(5)
$7 ==> 10
```

amazing, so we are able to quickly define functions and use them from within the shell, in general everything looks so smooth.

## Summary

After using modules in the previous tutorial we went on to investigate `java 9` by checking out it's `REPL`.  We started again from ground zero.  We have download from scratch `java9` then ran `jshell` the new `REPL` provided by `java 9`, We then ran some some calculations, saw the variable returned, saw the default imports by the shell and then defined quickly a function and used it, all without executing `javac` or `java` quickly within the `REPL` way to go java!