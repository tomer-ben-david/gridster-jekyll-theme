---
layout: post
title:  "Book review reactive domain modelling"
date:   2015-11-21 22:18:00
categories: functional-programming,domain-driven-design,data-science
comments: true
---
**Chapter 1** Global challenge overview, scalability, resiliency, maintainability.  The author prooves he has extensive experience, and has done his research on this area.  Many references to  many articles and good introduction to the subject.

**Chapter 2** 

1. Domain Modelling.  `SQL`, `NoSQL`, `normalized` and `denormalized` data, He debates with the question of when to store an `id` in a field and when the whole content, sometimes you want one kind and sometimes another.  Duplication is mostly avoided but there are situations its needed.  He also deals with the fact you don't really have joins in `NoSQL` databases.  Deals with the issue of a database which has fields getting more and more interconnected over time.  
1. A nice example for map reduce, you map each key to the year-month and then in the reduce you group by each such key so you can sum by 2015-10 and for each instance you sum it up.  Great example. 