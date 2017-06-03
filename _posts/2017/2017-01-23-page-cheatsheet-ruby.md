---
layout: post
title:  "Ruby cheasheet"
date:   2017-01-15 22:18:00
categories: cheatsheet,ruby
comments: true
---

```ruby
# mock power2 function.

def power2(x)
  x * x
end
require "test/unit"
require 'mocha/test_unit'

class TestSimpleNumber < Test::Unit::TestCase
  def test_simple
    expects(:power2).returns(3)
    assert_equal(4, power2(2) )
  end
end
```

[https://learnxinyminutes.com/docs/ruby/](https://learnxinyminutes.com/docs/ruby/)

