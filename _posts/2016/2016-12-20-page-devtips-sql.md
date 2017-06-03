---
layout: post
title:  "SQL cheasheet"
date:   2016-12-20 22:18:00
categories: cheatsheet,sql
comments: true
---
**In same query you can refer to same table twice**

```sql
select firstname, lastname 
  from names as a,
       names as b
  WHERE a.firstname = b.firstname
    AND a.lastname != b.lastname
```