---
layout: post
title:  "SBT for dummies"
date:   2015-12-18 22:18:00
categories: scala, devops
comments: true
published: true
---
## files

/--build.sbt
/--project/MyBuild.scala
/--src
/--main

**build.sbt** - main build script with DSL imports implicitly `sbt._` and `sbt.Keys._`
**project** folder - the build scripts and scala source are compiled to here + contains additional custom scripts you can create ex. `MyBuild.scala`

