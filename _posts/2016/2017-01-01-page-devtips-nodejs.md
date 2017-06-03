---
layout: post
title:  "NodeJS cheasheet"
date:   2017-01-01 22:18:00
categories: cheatsheet,nodejs
comments: true
---
**Remote debug nodejs app / use chrome developers to detect memory leaks**

**Step 1 enable remote debug on a running nodejs process**

```commandline
kill -SIGUSR1 $NODEJS_PID
```

Now your `nodejs` process (`V8`) is listening on port `5858`
 
**Step 2 run `node-inspector`**
 
```commandline
# node-inspector --web-port=8081 --save-live-edit
Node Inspector v0.12.8
Visit http://127.0.0.1:8081/?port=5858 to start debugging.
```

**memory leaks on nodejs**

[Alexkras.com on nodejs memory leaks](https://www.alexkras.com/simple-guide-to-finding-a-javascript-memory-leak-in-node-js/) does an excellent work on describing how to work on memory leaks on nodejs.

A brief summary from it:

1. Reproduce the problem - plot memory heap after triggering gc like every 2 seconds, see that memory increases:

```javascript
function generateHeapDumpAndStats(){
  //1. Force garbage collection every time this function is called
  try {
    global.gc();
  } catch (e) {
    console.log("You must run program with 'node --expose-gc index.js' or 'npm start'");
    process.exit();
  }
 
  //2. Output Heap stats
  var heapUsed = process.memoryUsage().heapUsed;
  console.log("Program is using " + heapUsed + " bytes of Heap.")
 
  //3. Get Heap dump
  process.kill(process.pid, 'SIGUSR2');
}
setInterval(generateHeapDumpAndStats, 2000); //Do garbage collection and heap dump every 2 seconds
```

2. 