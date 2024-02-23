---
layout: post
title: 'Weird Ruby: Nil Conversions'
date: 2024-02-23 09:15 +0100
tags:
- Weird Ruby
- Ruby
- Series
---

Most Rubyists probably know that `nil` in Ruby is an instance of the singleton
class [NilClass](https://ruby-doc.org/3.3.0/NilClass.html). Because `nil` is a
object like any other, we can actually invoke methods on it, and it turns out
that `NilClass` provides a few - mostly conversion methods to other Ruby
types. Let's see some of those in action:

``` ruby
irb(main):001:0> nil.to_h
=> {}
irb(main):002:0> nil.to_a
=> []
irb(main):003:0> nil.to_s
=> ""
irb(main):004:0> nil.to_f
=> 0.0
irb(main):005:0> nil.to_i
=> 0
irb(main):006:0> nil.to_c
=> (0+0i)
```

With those methods you can literally create something (e.g. an empty hash) out
of nothing! Whether you should actually be doing this is a totally different
matter, though. According to the class docs:

> The converter methods are carrying the concept of nullity to other classes.

I'm not sure how this "concept of nullity" is defined, but I've always found it
weird that you can equate the absence of value with some anchor value (e.g. an
empty collection or 0). I know there are contexts in which this may be useful,
but I'm fairly certain you'd be able to get similar results without resorting to
such conversions.

Keep in mind that you can do similar conversions in a different way:

``` ruby
irb(main):001:0> Array(nil)
=> []
irb(main):002:0> Hash(nil)
=> {}
irb(main):003:0> Integer(nil)
# `Integer': can't convert nil into Integer (TypeError)
irb(main):004:0> Float(nil)
# `Float': can't convert nil into Float (TypeError)
```

Notice how the "safe" number conversion methods `Integer` and `Float` operate differently and raise an exception when passed `nil`. That's part of the reason why it's often
suggested to avoid the use of `to_i` and `to_f` to avoid converting unexpected values.

The [Zen of Python](https://peps.python.org/pep-0020/) famously says:

> Explicit is better than implicit.

> Errors should never pass silently.

> In the face of ambiguity, refuse the temptation to guess.

But in the land of Ruby it seems we like to live dangerously!

That's all I have for you today. Keep Ruby weird!
