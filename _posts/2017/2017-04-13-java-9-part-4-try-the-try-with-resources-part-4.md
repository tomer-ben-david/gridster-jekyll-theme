---
layout: post
title:  "Java 9 Part 4 - Try the try with resources"
date:   2017-04-13 22:18:00
categories: java,java9,java 9,resources, tutorials, try with resources, design patters, software design
comments: true
published: false
---

## Introduction

This is part 4 of the `java 9` series.  While in part 3 we covered `interfaces` and the private methods that were added to them, in part 4 we see that `try-with-resoures` was enhanced, or we might say even fixed.  Try with resources which has been introduced back in `java 7`, a concept which helps developer close resources which are not using anymore such as `database connection`, `file streams`, etc.  So let's see how try with resources behaved in `java 7` and now in `java 9`.

## Step 1: Pre java 7 try with resources

Try with resources prior to java 9 meant that instead of just calling a piece of code which opens up a resource like:

```java
InputStream in = null;
try {
    // open some resource note we are inside the try block.
    input = new FileInputStream("somefile");
} finally {
    // close the resource.
    if (input != null)
        input.close();
}
```

As you see in the above example we have used a standard try and finally.

## Step 2: From java 7 and on try with resources

In java 7 we can put the opening of the stream itself inside the `try()` block note that now try has brackets - `try()`

So we use it as:

```java
try(FileInputStream input = new FileInputStream("somefile")) {
    
}
```

Where is the `close()` you ask? java will take care of that.  After all, it's `try with resources`.

## Step 3: From java 9 and on try with resources

If we have multiple resources and we are using them and we want try with resources to close them all now with java 9 its possible even without introducing a new variable, if our resource is already with the `final` keyword.  that is.

```java
final Resource resource1 = new Resource("resource1");
Resource resource2 = new Resource("resource2");

try(resource1; resource2 ...) { // couldn't do that before java 9
  // do something with the resources.
}
```

However now with java 9 we can:

```java
final Resource resource1 = new Resource("resource1");
Resource resource2 = new Resource("resource2");

try(resources1, resources2) { // possible with java 9
}
```

## Summary

In part 1 of this series we covered java 9 modules, in part 2, jshell, and then we went on to super interfaces in part 3.  In this part 4 we saw that `try-with-resources` was greatly enhanced to allow us to refer to mulitple seamlessly without the need to define any intermediate variable.  So you just `try-with-resources` seamlessly for multiple resources!