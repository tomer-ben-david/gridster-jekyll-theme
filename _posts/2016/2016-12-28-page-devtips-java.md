---
layout: post
title:  "Java cheasheet"
date:   2016-12-28 22:18:00
categories: cheatsheet,java
comments: true
---
```java
System.out.printf("%3d    ", n); // print number with spaces.
```


```java
Arrays.copyOfRange([1,2,3], 1, [1,2,3].length);  // subarray from 1 to length array

```

**Initialize 2 dim array**

```java
int[][] arr = new int[2][2];
```

**print two dim array**

```java
java.util.Arrays.deepToString(table)
```

**Read stdin**

```java
Scanner sc = new Scanner(System.in);
while (sc.hasNextLine()) {
    System.out.println(sc.nextLine());
} 
```
     
**spaced delimeted string to stream**
            
```java
import java.util.stream.*;
import java.util.regex.*;

Stream stream = Pattern.compile(" ").splitAsStream(scanner.nextLine()); // O(1)

// or simpler but would read the string completely and split to array // O(2)

Arrays.stream(sc.nextLine().split(" ")).forEach(item -> {});
```                

```java
// calculate total sum with streams.
int totalSum = Arrays.stream(lineAsStr.split(" "))
            .map(numAsStr -> Integer.valueOf(numAsStr))
            .reduce(0, (sum, n) -> sum += n);
```

## Resources

1. [Java Stream Tutorial](http://winterbe.com/posts/2014/07/31/java8-stream-tutorial-examples/)