---
layout: post
title: 'Weird Ruby: A Weird Way to Filter Out Elements'
date: 2024-01-16 12:06 +0200
tags:
- Ruby
- Weird Ruby
- Series
---

Imagine you want to filter out the `nil` elements in an array. There are many ways to do this in Ruby:

``` ruby
# common, but suboptimal
array.reject(&:nil?)
array.delete_if(&:nil?)
array.select { |e| !e.nil? }

# optimal
array.compact
```

But there's a slightly weirder way to achieve this, courtesy of the often
forgotten `Enumerable#grep_v` (the inverse of `Enumerable#grep`):

``` ruby
array.grep_v(nil)
array.grep_v(NilClass)
```

The last invocation might be somewhat surprising, but that works because `grep_v`
internally uses `===` (or rather its negation) to match against elements of the
receiver. That's why you can also use `grep_v` with ranges like this:

``` ruby
# take only the elements that are not between 2 and 5
(1..10).grep_v 2..5                #=> [1, 6, 7, 8, 9, 10]

# it takes an optional block as well
(1..10).grep_v(2..5) { |v| v * 2 } #=> [2, 12, 14, 16, 18, 20]
```

Kind of weird, but in an useful way. Same as `grep`, of course.

That's all I have for you today. Keep Ruby weird!
