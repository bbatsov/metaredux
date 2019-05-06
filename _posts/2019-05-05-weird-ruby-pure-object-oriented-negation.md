---
layout: post
title: 'Weird Ruby: Pure Object-Oriented Negation'
date: 2019-05-05 09:38 +0300
category: posts
tags:
- Ruby
- Weird Ruby
- Series
---

I'm kicking off a new series of posts dedicated to some fun and weird
aspects of our beloved Ruby programming language. I was torn apart between
"Fun Ruby" and "Weird Ruby" for the name of the series, but I've opted for the
latter for no real reason.

Today we're going to talk about object-oriented negation, which
probably sounds rather weird, right? Let's start with something
familiar. In Ruby negation typically looks like this:

``` ruby
!something.foo
not something.bar
```

Pretty straightforward stuff - you just use one of the negation
operators `!` or `not` and you're in business. But are they really
negation operators? Well, `not` certainly is, but `!` is actually a
rather ordinary method defined in the not so ordinary `BasicObject`
class.

``` ruby
# Try these expressions in a Ruby REPL like irb or pry.
method(:!)
# => #<Method: main.!>

method(:!).owner
# => BasicObject
```

`BasicObject` is at the root of Ruby's class hierarchy, which means
that methods defined in it are available in every Ruby class. Which
leads us to the twist - you can actually do negation in Ruby as a
regular method call:

``` ruby
something.foo.!
# double negation
something.bar.!.!

(x == y).!
(x == y).!.!
10.!
```

Admittedly that looks extremely weird, and it's pretty easy to misread
such code as `foo!` or 10 factorial.  I'm certainly not advocating
that someone should be writing such code - quite the opposite
actually.[^1] Still, it's fun how Ruby's purely object-oriented nature
exposes in a uniform manner functionality that's usually special in
most programming languages. Of course, most operators in Ruby are
actually regular methods (e.g. `+`, `-`, `==`, `!=`, etc) and this
gives us as programmers the flexibility to redefine them for
particular classes. Now let's abuse this knowledge to make `10.!`
compute factorial for real:

``` ruby
# Don't do this at home!
class Integer
  def !
    (1..self).reduce(:*)
  end
end

5.!
# => 120

!5
# => 120
```

Remember that writing code like this is a *bad* idea in general, but
it's cool that the option exists.

Note that `!something` and `something.!` are completely identical as
far as Ruby is concerned. Ruby has some special provisions[^2] for unary
and binary operator methods that allow for using them with a more
human-friendly (math inspired) notation:

``` ruby
something.foo.!
!something.foo

5.+(5)
5 + 5

x.==(10)
x == 10

x.<(5)
x < 5
```

That's all I had for you today! I hope it was weird enough, fun and
not totally useless! Keep hacking!

[^1]: We should probably add a rule in the [community Ruby style
    guide](https://github.com/rubocop-hq/ruby-style-guide) to advise
    against writing such code.
[^2]: Many people call this "syntax sugar".
