---
layout: post
title:  "My scalatest scalacheck flow"
date:   2015-01-04 22:18:00
categories: scala,functional-programming
comments: true
---
#### Introduction
1. I start with generating `simple values` required for my `class hierarchy`, In this phase I generate `Long` `Double` values.
1. I move forward to create `generators` for more complex `case classes`.
1. I create `Lists` of these `case classes`.
1. Finished modeling the `data structures` moving on to using them in `tests`!


##### Generate simple class
{% gist tomer-ben-david/3d575af90d67def63302#file-scalacheck-generate-simple-class-scala %}

##### Generate simple class frequencies

{% gist tomer-ben-david/dbe26012b7b3b30804e1#file-scalacheck-long-frequencies-scala %}

* Defined relative frequencies for arbitrary generated `longs` and for `Long.MaxValue` in this case `20/1`

##### Generate a complex class

{% gist tomer-ben-david/25a515a5b9262bc766fe#file-scalacheck-generate-complex-class-scala %}

* We used the `for` comprehension and in each `iteration` we call `Arbitrary(somegen).arbitrary` to get an .. `arbitrary` value we use this arbitrary value in order to `yield` our requested `data structure`.

##### General list of objects

Now that we know how to generate our `case class` lets generate a `list` of it.

{% gist tomer-ben-david/9382145c2f71553f669a#file-scalacheck-generate-list-of-objects-scala %}

we created a new `generator`, our new generator is calling `Gen.containerOf[List, OurType]` and passing as its `argument` our `single case class generator`.

