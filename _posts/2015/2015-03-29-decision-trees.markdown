---
layout: post
title:  "Decision Trees, entropy and information gain"
date:   2015-03-29 22:18:00
categories: machine-learning,algorithms
comments: true
---
#### Introduction
So you need to decide on something and wish to perform it the `machine-learning` way? you have come to the right `www` page.  `decision trees` are basically a bunch of questions you ask yourself (or the machine if you are a software developer) until you get to an prediction of what the answer is most likely to be according to the `decision tree` you or your machine have built.  Examples:
  
* Should we hire that candidate?
* Should I trust this Guy?
* Is the world coming to an end?

Let's take the third example as the most frequent question you might ask yourself.

So

#### Is the world comming to an end?

    nations-have-atom-bombs<Y/N
    |   some-have-atom-bombs: Y
    |   |   with-dictator-leader: Y
    |   |   |    the-end-of-the-world
    |   |   no-dictator-leader: N
    |   |   |    not-the-end-of-the-world

What you see is a tree we built, the tree is not nicely built from actual-data, usually we build it based on raw data we gather from the past collected data, this time, we just imagined-simulated a few worlds in some imagined-world-simulation game and saw the end results and from it decided on the `decision-tree`.
But how should you decide on a `decision-tree`?

You first need to decide on the first question.  For the first question you first need to decide which one will split the data to the most `purest` results.  By this we mean we examine all `features` we have `(have-atom-bombs, with-dictator-leader, cows-make-meow-instead-of-moo)`.  Now with all these features in hand we examine for *each and every* of them (`n features`), determine which split yields the purest outcome.  If you take this question then two different answers (binary tree) would claim the most purest (least `enthropical` ones) results - the best choice would be one who splits the data to two separate groups (comming-to-end and not-comming-to-end each in its own group - pure).  After you have chosen the first feature you then choose the next one among `n-1` features, then the next feature-question among `n-2` etc.
 You do this by calculating the `entropy` which measures the `impurity` for each feature result, you calculate it before and after the new `rule` or `decision-question` for each and every of the reminder features, the one which yields the highest `entropy` **reduction** is the one you choose for the next `decision-question`