---
layout: post
title: 'Weird Ruby: Zeroing in on a Couple of Numeric Predicates'
date: 2019-06-11 12:01 +0300
category: posts
tags:
- Ruby
- Weird Ruby
- Series
---

Today we're going to discuss a couple of really simple numeric predicates - namely
`Numeric#zero?` and `Numeric#nonzero?`. I assume most Ruby developers are familiar with them, as
they represent a popular alternative to doing traditional checks for zero:

``` ruby
if foo == 0 ...

if foo.zero? ...

if foo != 0 ...

if foo.nonzero? ...
```

So far, so good. Nothing really weird, right? Let's now take a closer at the two predicate functions:

``` ruby
0.zero?
# => true
0.nonzero?
#=> nil
1.zero?
#=> false
1.nonzero?
#=> 1
```

Now this looks kind of weird...
As you can see `zero?` returns `true` or `false` and `nonzero?` returns its receiver or `nil`.
So much for API symmetry. There's a [long weird story](https://bugs.ruby-lang.org/issues/9123#note-14) behind this inconsistency, and an [interesting ticket](https://bugs.ruby-lang.org/issues/9123) on Ruby's bug tracker discussing options how to address it.
Most of the time you probably won't experience any issues related to that inconsistency, but there are certainly cases where it is going to bite you.[^1]

As far as I'm concerned `nonzero?` doesn't exist and I simply use `!zero?` all the time. Negative predicates
are pretty weird in the first place and I've never seen much value in them.[^2]

I'll end this post with a small challenge for you - what are your favourite examples of API inconsistency in Ruby?

{% include series_listing.html site=site series="Weird Ruby" %}

[^1]: <https://github.com/rubocop-hq/rubocop/issues/3598>
[^2]: Probably the [community Ruby style guide](https://rubystyle.guide) should discourage those.
