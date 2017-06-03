---
layout: post
title:  "Scala flatMap and for comprehension"
date:   2017-05-24 22:18:00
categories: scala,functional-programming,scalding
link: https://stackoverflow.com/a/44182354/345859
comments: true
--- 
**Introduction**
I'm not a scala mega mind so feel free to correct me, but this is how I explain the `flatMap/map/for-comprehension` saga to myself!

To understand `for comprehension` and it's translation to `scala's map / flatMap` we must take small steps and understand the composing parts - `map` and `flatMap`.  But isn't `scala's flatMap` just `map` with `flatten` you ask thyself! if so why do so many developers find it so hard to get the grasp of it or of `for-comprehension / flatMap / map`. Well, if you just look at scala's `map` and `flatMap` signature you see they return the same return type `M[B]` and they work on the same input argument `A` (at least the first part to the function they take) if that's so what makes a difference?

**Our plan**

1. Understand scala's `map`.
1. Understand scala's `flatMap`.
1. Understand scala's `for comprehension`.`

**Scala's map**

scala map signature:

    map[B](f: (A) => B): M[B]

But there is a big part missing when we look at this signature, and it's - where does this `A` comes from? our container is of type `A` so its important to look at this function in the context of the container - `M[A]`.  Our container could be a `List` of items of type `A` and our `map` function takes a function which transform each items of type `A` to type `B`, then it returns a container of type `B` (or `M[B]`)

Let's write map's signature taking into account the container:

    M[A]: // We are in M[A] context.
        map[B](f: (A) => B): M[B] // map takes a function which knows to transform A to B and then it bundles them in M[B]

Note an **extremely highly highly important fact about map** - it bundles **automatically** in the output container `M[B]` you have no control over it.  Let's us stress it again:

1. `map` chooses the output container for us and its going to be the same container as the source we work on so for `M[A]` container we get the same `M` container only for `B` `M[B]` and nothing else!
1. `map` does this containerization for us we just give a mapping from `A` to `B` and it would put it in the box of `M[B]` will put it in the box for us!

You see you did not specify how to `containerize` the item you just specified how to transform the internal items.  And as we have the same container `M` for both `M[A]` and `M[B]` this means `M[B]` is the same container, meaning if you have `List[A]` then you are going to have a `List[B]` and more importantly `map` is doing it for you!
 
Now that we have dealt with `map` let's move on to `flatMap`.

**Scala's flatMap**

Let's see its signature:

    flatMap[B](f: (A) => M[B]): M[B] // we need to show it how to containerize the A into M[B]

You see the big difference from map to `flatMap` in flatMap we are providing it with the function that does not just convert from `A to B` but also containerizes it into `M[B]`.

**why do we care who does the containerization?**

So why do we so much care of the input function to map/flatMap does the containerization into `M[B]` or the map itself does the containerization for us?

You see in the context of `for comprehension` what's happening is multiple transformations on the item provided in the `for` so we are giving the next worker in our assembly line the ability to determine the packaging.  imagine we have an assembly line each worker does something to the product and only the last worker is packaging it in a container! welcome to `flatMap` this is it's purpose, in `map` each worker when finished working on the item also packages it so you get containers over containers.

**The mighty for comprehension**

Now let's looks into your for comprehension taking into account what we said above:


    def bothMatch(pat:String,pat2:String,s:String):Option[Boolean] = for {
        f <- mkMatcher(pat)   
        g <- mkMatcher(pat2)
    } yield f(s) && g(s)


What have we got here:

1. `mkMatcher` returns a `container` the container contains a function: `String => Boolean`
1. The rules are the if we have multiple `<-` they translate to `flatMap` except for the last one.
1. As `f <- mkMatcher(pat)` is first in `sequence` (think `assembly line`) all we want out of it is to take `f` and pass it to the next worker in the assembly line, we let the next worker in our assembly line (the next function) the ability to determine what would be the packaging back of our item this is why the last function is `map`.
1. The last `g <- mkMatcher(pat2)` will use `map` this is because its last in assembly line! so it can just do the final operation with `map( g => ` which yes! pulls out `g` and uses the `f` which has already been pulled out from the container by the `flatMap` therefore we end up with first:


    mkMatcher(pat) flatMap (f // pull out f function give item to next assembly line worker (you see it has access to `f`, and do not package it back i mean let the map determine the packaging let the next assembly line worker determine the container.
    mkMatcher(pat2) map (g => f(s) ...)) // as this is the last function in the assembly line we are going to use map and pull g out of the container and to the packaging back, its `map` and this packaging will throttle all the way up and be our package or our container, yah!

