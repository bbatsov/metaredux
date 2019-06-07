---
layout: post
title: 'Weird Ruby: Double Negation'
date: 2019-05-10 10:53 +0300
category: posts
tags:
- Ruby
- Weird Ruby
- Series
---

Welcome to another installment of the "Weird Ruby" series!
Today we're going to continue exactly where we left off [last time]({% post_url 2019-05-05-weird-ruby-pure-object-oriented-negation %}).
In other words - we're going to discuss this bit of slightly weird Ruby code:

``` ruby
!!foo
```

This is a somewhat popular idiom to convert a value
to a boolean (`true` or `false`).[^1] Believe it or not the idiom is known
as "double negation". Let's take a look at how this behaves:

``` ruby
!!10
# => true

!!true
# => true

!!false
# => false

!!nil
# => false
```

There's nothing really special about the code once you know that `!` is just a regular method in
Ruby. Double negation is nothing but a syntactic sugar for:

``` ruby
foo.!.!
```

It's important to understand that `!!` are just two negations combined and not some special operator. I've seen
people confused about this, so I wanted to highlight it here.

Another source of confusion about double negation is that in some programming languages you can't use `!` with non-boolean values:

``` jav
// Java
// syntax error
var foo = 5;
!!foo;

// double negation works only for boolean values
// which renders it useless
var foo = true;
!!foo;
```

So, now we understand how `!!` works, but you might be wondering if it's any useful.
After all, we know that in Ruby every object except `false` and `nil` is logically true (a.k.a. `truthy`). This means it's unlikely
we'll need to convert objects explicitly to boolean values. Just consider this trivial example:

``` ruby
# redundant conversion
do_something if !!foo

# idiomatic Ruby
do_something if foo
```

In practice there's just one place where we really want to convert something to a boolean and that's the return value of
predicate methods. While it's not strictly mandated by Ruby, there's a strong convention that a predicate method should
return `true` or `false` and the `!!` trick is useful there:

``` ruby
def awesome?
  # ...
  !!result
end

# an alternative approach
def awesome?
  # ...
  !result.nil?
end
```

I've never had any issues writing `!foo.nil?`, so I rarely use `!!`, but the option is there if you need/like it.

This discussion reminded me that it would have been nice if Ruby had a `to_bool` method or something like this. Something as simple as:

``` ruby
class Object
  def to_bool
    !!self
  end
end

5.to_bool
# => true

nil.to_bool
# => false
```

Simple enough, but probably an overkill given the limited usage of "real" boolean values in Ruby.

That's all for today! I hope it was weird and useful enough for you! Keep hacking!

[^1]: This idiom is also popular in the JavaScript world.

{% include series_listing.html site=site series="Weird Ruby" %}
