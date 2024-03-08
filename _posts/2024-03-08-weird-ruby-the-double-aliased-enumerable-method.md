---
layout: post
title: 'Weird Ruby: The Double Aliased Enumerable Method'
date: 2024-03-08 09:58 +0200
tags:
- Ruby
- Weird Ruby
- Series
---

Ruby is famous (infamous?) for giving us many ways to do the same thing.
One aspect of this is that quite a few methods in core classes have aliases, especially
in the `Enumerable` module. E.g.:

- `collect` -> `map`
- `inject` -> `reduce`
- `detect` -> `find`
- `select` -> `find_all`

Most Rubyists are probably aware that the "original" method names (`collect`,
`inject`, etc) were inspired by Smalltalk and later the aliases where added to
reflect the naming trends in the functional programming community.
`Enumerable#select` is kind of special, as since Ruby 2.6 it has a second alias
named `filter`, thus making the only Ruby method I can think of in this elite
category. As a reminder here's how the methods behave:

``` ruby
(1..10).find_all { |i| i % 3 == 0 }   #=> [3, 6, 9]

[1,2,3,4,5].select { |num| num.even? }   #=> [2, 4]

[:foo, :bar].filter { |x| x == :foo }   #=> [:foo]
```

I think today `select` is the most popular of the 3, but I can imagine that over time
`filter` will give it a run for its money.[^1]

The usage of aliases at this level has always been a bit controversial in the Ruby community. On one hand you make it easier for people coming from different backgrounds to find familiar methods, but on another you also confuse people a bit, as it's not obvious that the different names are aliases. I've seen plenty of people asking what's the difference between `inject` and `reduce` and so on.

What's your preferred method to filter elements? Can you think of any other methods in the Ruby core classes that have multiple aliases?[^2] Please, share those in the comments!

That's all I have for you today. Keep hacking!

**P.S.** As someone commented, `Hash#include?` is even more curious (weird?) as it has 3 (!!!) aliases:

- `has_key?`
- `key?`
- `member`?

Will we be able to top this?

[^1]: I believe that `filter` is the terminology used in most programming languages.
[^2]: `Enumerable#filter!` does not count! ;-)
