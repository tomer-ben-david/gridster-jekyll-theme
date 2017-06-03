---
layout: post
title:  "Python cheasheet"
date:   2016-12-27 22:18:00
categories: cheatsheet,python
comments: true
---
**Get content of URL**

```python
import urllib2
print urllib2.urlopen('http://google.com').read()
```

**Remove chars and split by , from a string convert to list**

```python
'jshfjshfjsh[]'.translate(None, '[] "\n').split(',')
```

**Diff between two arrays**

```python
set(['first', 'second', 'third']) - set(['first', 'second'])
```

