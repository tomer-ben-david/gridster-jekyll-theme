---
layout: post
title:  "Scala ad hoc polymorphism explained"
date:   2017-03-13 22:18:00
categories: dev,scala,functional-programming
comments: true
published: false
---

## Introduction


There are different types of `Polymorphism`, namely:

1. `Subtype Polymorphism`: Remember `OOP`? yes that's the one from there.
2. `Parametric Polymorphism`: Generics! with type parameters.  A function or a data type will handle the same way values with different types.
3. `Ad hoc polymorphism`: it's also based on generics so it's similar to `Parametric Polymorphis` but with a twist (see below).

## Parametric VS Ad Hoc Polymorphism

So we have 3 types of polymorphism, isn't that just polymorphic :) `subtype` we know from good old `OOP`.  The difference between `Parameteric` and `Ad Hoc` is intriguing.  They both use `generics` in order to allow code to run on different type.  So when you have `Parametric` Polymorphism you define some shared functionality and you have a `type parameter` now whenever run this code and supply the `type parameter` this shared code would run with this `type parameter`  The difference however between this and `Ad Hoc Polymorphism` is that `Ad Hoc Polymorphism` "examines" the types **ad hoc polymorphism is utilizing a possibly different implementation based on the types**.  So in standard `Parametric Polymorphism` it's the same code running on different types and in `ad hoc polymorphism` it would be different code based on the actual type.
 
 ## Parameteric Polymorphism
 
 Well, parametric polymorphism, is as it name says, just parametric.  Meaning, we specify a function with a generic parameter, then when we instantiate that function or whatever, we specify our current type and the current type would get applied by the function that't it no trick!
 
 Let's see an example:
 
 ```scala
 object GenericPolyPlayground {
 
   trait GenericStack[T] {
     var elms: List[T] = Nil
     def push(x: T) { elms = x :: elms }
     def peak(): T = elms.head
     def pop(): T =
     {
       val h = elms.head
       elms = elms.tail
       h
     }
   }
 }
 ```
 
So what do we have there, we have a generic implementation of a `stack`, we don't specify what type that stack is, and it will always run the operations on a generic list like `head` `tail` so whatever we put in that `GenericStack` would get placed on a generic `List` and thus would run the same operations.  Le's try it.

```scala
    val intStack = new GenericPolyPlayground.GenericStack[Int] {}
    intStack.push(1)
    println(s"with generic polymorphism it will just poop 1, so boring: ${intStack.pop()}") // it always has the same pop implementation.
    
    // Output:
    
    // with generic polymorphism it will just poop 1, so boring: 1    
```

So you see, we pushed a `1` and it printed `1` with pop nothing specia.

## Ad Hoc Polymorphism

With ad hoc polymorphism we can actually `change` the behaviour of the generic code based on the type we polymorph on! let's see an example:

```scala
object AdHocPolyPlayground {

  trait AdHocStack[T] {
    var elms: List[T] = Nil

    def push(x: T) {
      elms = x :: elms
    }

    def peak(): T = elms.head

    def pop()(implicit crazy: Crazy[T]): T = {
      crazy.omg()
      val h = elms.head
      elms = elms.tail
      h
    }
  }

  trait Crazy[T] {
    def omg()
  }

  implicit val crazyInt: Crazy[Int] = new Crazy[Int] {
    def omg() {
      println("omg i'm crazy")
    }
  }

  implicit val crazyLong: Crazy[Long] = new Crazy[Long] {
    def omg() {println("omg im so long!") }
  }
}
```

In the above implementation we call an additional function from within `pop` generic implementation we call: `crazy.omg()`;

and that `crazy.omg` is different based on the type you see we have one `omg` for `Int` and another for `Long` so our generic implementation changes according to the type we polymorph on see the below example:

```scala
    val intCrazyStack = new AdHocPolyPlayground.AdHocStack[Int] {}
    intCrazyStack.push(1)
    println(s"with ad hoc polymorphism it will initiate crazy stuff for type int! we change behaviour based on type: ${intCrazyStack.pop()}")

    val longCrazyStack = new AdHocPolyPlayground.AdHocStack[Long] {}
    longCrazyStack.push(1)
    println(s"with ad hoc polymorphism it will initiate a different crazy stuff for long! we change generic behaviour based on type: ${longCrazyStack.pop()}")

  }
  
  // and the output:
    
  omg i'm crazy
  with ad hoc polymorphism it will initiate crazy stuff for type int! we change behaviour based on type: 1
  omg im so long!
  with ad hoc polymorphism it will initiate a different crazy stuff for long! we change generic behaviour based on   type: 1
  
```

## Summary

We have 3 types of polymorphism, subtype, adhoc and generic.  Subtype is the standard OOP polymorphism.  With generic we specify a generic code to run on various types and with ad hoc polymorphism we can change the generic code run based on the actual type polymorphed on! :) 