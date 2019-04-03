---
layout: post
title: Ruby's Creed
date: 2019-04-02 11:14 +0300
categories: posts
tags:
- Ruby
- Language Design
- Meta
---

Every programming language has some fundamental idea(s) that drives
its design and evolution.  For Haskell that's functional purity and
state-of-the-art static typing, for Erlang that's distributed
programming and fault tolerance, for Clojure that's simplicity and
stability. What all these examples have in common is that they are
relatively easy to understand and map to design decisions in the
languages. This, in turn, makes it possible to determine down the road whether
a language sticks to its core values or deviates from them.

Ruby, however, is very different.  It's world famous for its unique
creed - "optimizing for programming happiness".  But what does this
really mean? How does one optimize for happiness?

Unfortunately I've never heard Matz speak about this, so I'll offer
you my perspective instead - happiness is simplicity, readability,
consistency, expressiveness and flexibility.  If I have to describe
happiness via Ruby code I'd probably do it like this:

``` ruby
3.times do
  puts "Ruby Rocks!"
end
```

<!--more-->

Having numbers that are just regular objects, instead of some special
primitive type is very powerful. Having code structure that's
somewhat intuitive even to non-programmers is very powerful.

I remember how amazed I was, when I was starting out with Ruby, that
everything was an object - even things like `true`, `false` and
`nil`.[^1] I remember how impressed I was with the opportunities that
came with metaprogramming and how skillfully it was employed at the
core of the language. Ruby also brought to the table a reasonable blend of
object-oriented and functional programming techniques, even if it
never really tried to promote a functional programming style.

Ruby certainly didn't feel perfect, but felt really good. It was way
ahead of most of the competition. A fun blend of Java and
Lisp. Practical, powerful, expressive and flexible. And fun!

In recent years, however, I've been progressively more and more
concerned with the direction in which Ruby has been heading when it
comes to language design (think Ruby's syntax). Here's a list of
changes and additions that I certainly never needed (or wanted):

* Ruby 1.9 hash literals
* Refinements
* `%i` literals
* Rational/Complex literals (`2/3r`, `2+1i`)
* Endless ranges (`1..`)
* Safe navigation operator (`&.`)

Not all of those were bad additions in my book, though. Just
unnecessary. I felt really strongly only about the hash literals and
the refinements, as they brought a fair amount of complexity with
them. Creating a special hash syntax that could express only a subset
(although quite large) of all possible hash literals felt very
short-sighted to me. I know that syntax really took off, but I still
don't like it. I'm used to it, but I don't like it. I won't say
anything about refinements - I think their almost non-existing
adoption is the perfect case style of their usefulness.

The other changes on my list had small impact and fit well with the
existing language design, so I didn't really care about their addition
much. Still, I was disappointed when the safe navigation operator as
introduced, as it basically acknowledged that it's fine to return
`nil`.

For the sake of completeness, here are some changes that I really enjoyed:

* Lambda literals (a.k.a. stabby lambdas)
* The ability to use symbols as procs
* Keyword parameters
* Squiggly heredocs (`<<~`)
* Frozen string literals pragma
* Making UTF-8 the default source code encoding
* Unifying `Integer` and `Fixnum`

What all of them have in common what they solve actual problems in an
unobtrusive way. I know that this is all very subjective, of course, but
somehow I felt that all them fit very well in the rest of Ruby.
Judging by the feedback all of them received and the wide adoption they got,
I assume many people enjoyed them as well. Then again - Ruby 1.9 hashes are
widely adopted as well. :-) I hope you get my point.
Now back to the my frustrations.

The tipping point for me, however,
was just a couple of weeks ago when it was announced that Ruby 2.7
would feature the following syntax for named block parameters:

``` ruby
# now
[1, 2, 3].each { |i| puts i }
(1..10).map { |i| i * 3 }
(1..9).each_slice(3).map { |x, y, z| x + y + z }

# Ruby 2.7
[1, 2, 3].each { puts @1 }
(1..10).map { @1 * 3 }
(1..9).each_slice(3).map { @1 + @2 + @3 }
```

I don't know about you, but I really don't think that saving a couple
of characters is worth introducing special syntax. Not to mention a
syntax that reduces to some extent the readability of the code.

If you push this to the extreme, consider the following example:

``` ruby
h = Hash.new { |hash, key| hash[key] = "Go Fish: #{key}" }

# vs

h = Hash.new { @1[@2] = "Go Fish: #{@2}" }
```

This new syntax was embraced by some, but it also was heavily criticized
by others (including yours truly). There's even an ongoing
["petition"](https://bugs.ruby-lang.org/issues/15723) to make Matz
reconsider its addition (or at least its current form). I sincerely
hope that by the time you've finished reading this article you'll
support it as well.

There are several things about this syntax I dislike:

* As the new named variables look like instance variables this adds a bit of
  cognitive overhead (and might break the syntax-highlighting in certain
  editors, which had a special coloring for instance vars, based on their `@` prefix).
* It hides the number of parameters yielded to the block.
* It promotes the use of cryptic names.


Sure, in simple cases the new syntax looks OK:

``` ruby
names.map { @1.upcase }
names.each { puts @1 }
```

But why force people to learn new syntax for something as simple as
that. Especially given the existence of other shorthand notations?

``` ruby
names.map(&:upcase)
# This will be simplified in Ruby 2.7
names.map(&method(:puts))
```

So, we've got a new syntax that's not shorter than what we can
currently do (in the most command case with one parameter), and is not
really as expressive as the "classic" syntax.

I also find it amusing that Ruby 2.7 introduced another similar
feature, that's more in line with what have been promoted so far -
namely the method reference operator (`.:`). Its main usecase covers
one of the typical usecases for the numbered parameters:

``` ruby
[2, 4, 8, 16, 32].map { |n| Math.log2(n) }

[2, 4, 8, 16, 32].map { Math.log2($1) }

[2, 4, 8, 16, 32].map(&Math.:log2)
```

By the way, while `.:` doesn't really get any awards for code elegance, it least it's aligned
with another existing pattern in Ruby. Victor Shepelev echoes my sentiment almost
precisely [here](https://bugs.ruby-lang.org/issues/15723#note-44).

Now back to numbered block parameters.  I really want someone to explain
to me how a language change like this one fits with "optimizing for
happiness". What is the problem we had to solve that required the
inclusion of syntax that's far from ideal (as acknowledged by Matz
himself)? Why did suddenly a 7 year old ticket went into development?

And, of course, don't forget the extra impact of every language
change. Now tools like [parser](https://github.com/whitequark/parser)
and [RuboCop](https://github.com/rubocop-hq) have to account for the
new syntax. The [Ruby Style
Guide](https://github.com/rubocop-hq/ruby-style-guide) should advise
when it's appropriate to use this and when it's not. RuboCop should
automate those suggestions into cops. Code that uses the new feature
won't be backwards compatible. And then we're stuck with it
forever.[^2] So much hassle for something so trivial that literally solves
nothing.[^3]

If that's what passes for "optimizing for happiness" these days I think
we've got a serious problem.

## Epilogue

This post is not intended as a rant. It's intended to be a
conversation starter.  I want to make it crystal clear that I'm not
one of those people who'd just argue against innovation just for the
sake of arguing. I understand that this syntax has some limited
application here and there, and I understand that there might be
people who even find it appealing and highly desirable. But I'm also
certain that this feature can be implemented better than it currently
is.[^4]

In my opinion half-baked micro-optimizations add nothing, but
complexity in the long run, and should be avoided. I'm really worried
that Ruby has been gradually losing its way and has strayed from its
creed. I hope this post will be a wake up call for some of you and
will gain the attention of the Ruby Core Team as well.

I'm not a believer in languages designed by a committee and I have
faith in Matz making reasonable decisions at the end of the
day. However, I'm also a big believer in sticking to your values,
developing a language that's consistent and elegant. Random additions
here and there stick out like sore thumbs, and are a recipe for frustration
and friction in the long run.

Matz has been talking a lot in recent years about the big initiatives
on the horizon (a.k.a. Ruby 3.0) like duck typing, faster performance
(Ruby 3x3), introducing a better concurrency API (Guilds), repackaging
the standard library, and the tools surrounding Ruby.  I haven't heard
much in the direction of evolving Ruby from a syntactic perspective,
though, and I'm left wondering if there's any strategy there at all,
or we're just figuring things as we go.  Actually, I lied to you -
once I listened to Matz discuss for quite a while why it's a good idea
to add `then` as an alias to `yield_self` and this worried me even
more.

Ruby actually has real problems to solve, and it'd be really great if
someone focused on those. To wrap it up, I'd like to go back to the
very beginning - Ruby's creed. Here's how the language is described on
Ruby's official web site:

> A dynamic, open source programming language with a focus on
> simplicity and productivity. It has an elegant syntax that is
> natural to read and easy to write.
>
> -- ruby-lang.org

I think everyone suggesting new Ruby functionality and everyone from
Ruby's Core Team should remind themselves of this
occasionally. Simplicity and elegant syntax are not things that
happens by themselves - they require a lot of hard work and
consideration. Now let's go back to optimizing for happiness once
again!

**P.S.** I'd really love to hear what do you think about all of
this. Do you like all the language changes made in Ruby in the 2.x
series? What's the meaning of "optimizing for happiness" for you?
Where do you think Ruby really needs to evolve in terms of syntax?

[^1]: Which are singleton instances of `TrueClass`, `FalseClass` and `NilClass` respectively.
[^2]: The flip-flop operator (`..`) has been dying a slow death for many years now. Backwards compatibility makes most syntax constructs practically eternal.
[^3]: On the bright side - we get a new favourite bike-shedding topic.
[^4]: I believe that a special global variable would probably be a better solution. At least it's more in line with what Ruby has been doing in the past (e.g. regex matches and `$1`, `$2`, etc).
