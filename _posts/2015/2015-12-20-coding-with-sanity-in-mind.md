---
layout: post
title:  "Coding with sanity in mind"
date:   2015-12-18 22:18:00
categories: software-development,architecture,design
comments: true
published: false
for: lp
---
When coding you usually hopefully faced with plenty of dillemas.  In this post we would try to untangle some of them.  

## Creating objects for further storage

You get some input, you need to create another object and then persist that different object to data store.  How to name the method that creates that other object? `create`? nah, that also has the meaning of CRUD like create.  what we are actually doing here is building or constructing another object.  Therefore you need to have another class with a buildThatOtherObject method that would receive the input and return (no side effect) that new object built.  where would you place that builder? in the same package space where you are building it? nah, we are going to try and put the same funcitonality under same package convention, it would be best if all functionality of some specific feature would be in the same package so in best case if you want to get rid of that functionality you just delete that pacakge and the feature is gone :) .  It's a little too intentsive in this point in software development world so we would just place it under 

`util --> functionality-name --> YourBuilderInterface.class`  in java8 this can be an interface with default method.  In `scala` that would be a `trait`.

so we have here two conventions if you are creating an object out of another object call that method SomethingBuilder.