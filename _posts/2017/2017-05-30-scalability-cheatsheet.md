---
layout: post
title:  "Scalability and performance cheatsheet"
date:   2017-05-30 22:18:00
categories: scalability
comments: true
--- 
* **Boxing and unboxing** cost might be high especially when in a loop (can create many heap allocations).

* **Tail recursion** If you find yourself in a situation where you are doing a recursive call (where you should first find for already existing library function to do the operation for you instead of recursive call), then do whatever possible for the recursive call to be `tail recursive`.  A function is `tail recursive` if the recursive call is the last instruction performed which means only first call is in `stack`.  A hint for many tail recursive call is that you pass some accumulator inside the recursive call, which means you are doing the calculation in the next step in the recursion as opposed to holding the accumulator outside the tail recursive call.

```scala
def tailrecSum(l: List[Int]): Int = {
  def loop(list: List[Int], acc: Int): Int = list match {
    case Nil => acc
    case x :: xs => loop(xs, acc + x) // you see we pass the accumulator to the next recursive call. no calc after loop.
  }
  loop(l, 0) 
}
```

* **Views** offers a way for a computation to be deferred downstream the computation, add `view` to an eagerly evaluation collection that allows it.  Ex. `val listView: SeqView[Int, List[Int]] = List(1, 2, 3).view`

* **Event Sourcing** describes an architectural approach to designing systems that relies on processing events over time instead of relying on a model of the current state.

* **blocking {}** (scala) construct is used to notify the ExecutionContext that a computation is blocking. This allows the ExecutionContext to adapt its execution strategy. For example, the default global ExecutionContext will temporarily increase the number of threads in the pool when it performs a computation wrapped with blocking. A dedicated thread is created in the pool to execute the blocking computation, making sure that the rest of the pool remains available for CPU-bound computations.

* **CRDT** stands for Conflict-free Replicated Data Types.  A CRDT is a data structure that is specifically designed to ensure eventual consistency across multiple components without the need for synchronization.  CRDTs are defined to make conflicts mathematically impossible.  To be defined as a CRDT, a data structure has to support only commutative updates. That is, regardless of the ordering in which the update operations are applied, the end state must always be the same.  When a system uses CRDTs, all the nodes can send each other update messages without a need for strict synchronization. The messages can be received in any order, and all the local states will converge to the same value eventually.

* **Eventual consistency** is a well-known concept in distributed system, which is not exclusive to CRDTs. This model guarantees that eventually, if a piece of data is no longer modified, all nodes in a cluster will end up with the same value for this piece of data. Nodes send each other update notifications to keep their state synchronized. The difference with strong consistency is that at a given time, some nodes may see a slightly outdated state until they receive the update notice.  All the nodes of the cluster hold the same piece of data (A = 0). Node 1 receives an update to set the value of A to 1. After updating its internal state, it broadcasts the update to the rest of the cluster. The messages reach their targets at different instants, which means that until we reach step 4, A has a different value depending on the node. If a client queries node 4 for the value of A at step 3, they receive an older value as the change has not yet been reflected in node 4.  This is the secret of eventual consistency without merge conflict.

**References**

* Scala High Performance Programming Book by Michael Diamant and Vincent Theron

Originally published at: []https://devatrest.blogspot.com/2017/05/performance-and-scalability-cheatsheet.html](https://devatrest.blogspot.com/2017/05/performance-and-scalability-cheatsheet.html)