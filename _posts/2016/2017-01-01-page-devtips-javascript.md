---
layout: post
title:  "JavaScript cheasheet"
date:   2017-01-01 22:18:00
categories: cheatsheet,javascript
comments: true
---
**Add `replaceAll` function to existing String object**

```javascript
String.prototype.replaceAll = function(search, replacement) {
    var target = this;
    return target.replace(new RegExp(search, 'g'), replacement);
};
```

**Tracing full stacktace with promises**

Use `bluebird` which encapsulates `ecmascript2016` `promises` and call `Promise.longStackTraces();`

or

in chrome devtools enable the `Async` checkbox and youl would see full back stacktraces in error happens.