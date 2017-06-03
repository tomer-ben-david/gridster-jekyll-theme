---
layout: post
title:  "Scala map and flatMap the missing explanation"
date:   2015-12-09 22:18:00
categories: scala, functional-programming
comments: true
published: true
---
**Map**

1. you provide a function to map `item -> item`
2. map scans every item in the input container
3. map applies your function to each item
4. map creates the same container as was originally provided (`List` for `List`) and wraps the results in it.

**flatMap**

1. you provide a function to map `item -> Container[Item]` so `flatMap` expects you to create a container for each item you scan
2. flatMap then creates the same external container
3. flatMap then strips each item from the container it's in and adds it to the external container which it's creating the same - as the original container provided.