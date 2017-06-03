---
layout: post
title:  "Troubleshooting storm"
date:   2015-11-09 22:18:00
categories: devops, programming, functional-programming
comments: true
published: false
for: lp
---
The history of software development wars.
---------

1. In functional programming you use functions.  You then compose smaller functions into larger ones.  So you compose functions.
1. When you compose functions you sometimes have some kind of a context, lets say a database.  So each function takes the return value of the previous function runs an operation on database with the prev value.
1. As we said we first compose all the functions but what about the context? The `context` can be passed with `currying` in this case you compose the functions and you don't pass the last database parameters.  So now all your functions return type of `DatabaseConn => Value`.  But what if you don't just have such functions to compose i mean what if one of the functions is not a `function` but an `Option`.  In this case we encapsulate the `Option` in `Reader Monad` the `Reader monad` would just be a class encapsulating an operation to perform the operation would be `DatabaseConn => Option[Value]` and you can run it because it receives as all previous operations receive a DatabaseConn. 
1. But what if you wish to compose the functions into an Option.  an `Option` is not a function.  In this case the reader monad can help you.
1. When you use the `reader monad` pattern, you encapsulate your logic you wish to run (the `Option` logic for example).  So as you want to compose the `Option`
1. Need to add an example.