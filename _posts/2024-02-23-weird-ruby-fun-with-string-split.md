---
title: 'Weird Ruby: Fun with String#split'
date: 2024-02-23 14:05 +0100
tags:
- Ruby
- Weird Ruby
- Series
---

`String#split` is a pretty-well known and commonly used method. Still, its behaviour
in some cases might surprise you:

``` ruby
irb(main):001:0> 'foo.bar'.split('.')
=> ["foo", "bar"]
irb(main):002:0> '...'.split('.')
=> []
irb(main):003:0> 'foo...'.split('.')
=> ["foo"]
irb(main):004:0> 'foo'.split('.')
=> ["foo"]
irb(main):005:0> ''.split('.')
=> []
```

No comment needed. Keep Ruby weird!
