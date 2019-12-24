---
layout: post
title: 'Weird Ruby: Array Multiplication'
date: 2019-12-24 10:47 +0200
category: posts
tags:
- Ruby
- Weird Ruby
- Series
- Meta Advent 2019
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

So, we've made it to the final day of this year's advent and to the final day of
my "Meta Advent" blogging marathon! Time to examine another fun Ruby weirdness!

Most Rubyists probably know that they can use `Array#*` to "multiply" an array:[^1]

``` ruby
arr = [1, 2, 3]
arr * 3 # => [1, 2, 3, 1, 2, 3, 1, 2, 3]
```

Nothing weird so far. `Array#*`, however, can do a bit more than this - its behaviour
changes drastically when you pass a string instead of a number to it:

``` ruby
arr * " " => "1 2 3"
arr * "," => "1,2,3"
```

Seems now it behaves just like `Array#join`. Weird, right?
I'd strong advise against using `Array#*` instead of `Array#join`, but it's a cool
little trick to know and a fine example of Ruby's extreme flexibility.

That's all I have for you today! Enjoy the holidays!

[^1]: I can't remember when was the last time I needed to do something like this.

{% include series_listing.html site=site series="Weird Ruby" %}
