---
layout: post
title: 'Weird Ruby: For Loops'
date: 2019-12-18 20:33 +0200
category: posts
tags:
- Ruby
- Weird Ruby
- Series
- Meta Advent 2019
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

Another day of the advent, another short "Weird Ruby" installment.
Today's topic are `for` loops in Ruby. And no, we're not going to be
discussing how to emulate C-style `for` loops in Ruby. You know, the ones
that look like:

``` c++
for (int i = 0; i < array.length; i++) {
  // something
}
```

Instead we're going to focus on Ruby's own flavour of `for` loops.
Did you even know about Ruby's `for` loops? I won't blame you if you
didn't, as almost no one uses those in practice. I've been doing
Ruby programming for over a decade now and I've yet to see someone
using `for` in the projects I've worked on. That's how `for` loops look:

``` ruby
for element in array do
  # do something with element
end
```

That's pretty similar to the much more common:

``` ruby
array.each do |element|
  # do something with element
end
```

If the two snippets are more or less the same then why is everyone using `each`?
Yeah, `each` is a regular method in `Enumerable` and `for` is a special
syntactic construct, but that's not that big of a difference in practical terms,
right?[^1] There must be something weird going on. Indeed, there is! The main
difference between the two approaches is best illustrated with a short code
example:

``` ruby
arr = [1, 2, 3]

arr.each { |elem| puts elem }

elem # => NameError: undefined local variable or method `last' for main:Object

for elem in arr
  puts elem
end

elem # => 3
```

As you can see `for` loops don't introduce a new block scope and variables
created inside them (including the binding for the current element) continue to
exist after the `for` loop is done. This is reminiscent of the behavior of
blocks before Ruby 1.9.

``` ruby
# Ruby 1.8
arr = [1, 2, 3]

arr.each { |elem| puts elem }

elem # => 3

arr.each { |arr| puts arr }

arr # => 3

# We have to recreate the array
arr = [1, 2, 3]

for arr in arr
  puts arr
end

arr # => 3
```

Thankfully blocks behave in a much saner manner today, unlike `for` loops.
I've got no idea why someone decided that this behavior
is desirable and I find it pretty weird that it wasn't updated when the
semantics where changed for regular blocks.

That's all I have for you today! See you again tomorrow!

[^1]: In fact internally `for` is implemented in terms of `each`.

{% include series_listing.html site=site series="Weird Ruby" %}
