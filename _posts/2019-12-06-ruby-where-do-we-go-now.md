---
layout: post
title: Ruby, Where do We Go Now?
categories: posts
tags:
- Ruby
- Language Design
- Meta
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

A while ago I wrote [an article]({% post_url 2019-04-02-ruby-s-creed %}), where
I made the case Ruby seems to have lost its way. I thought I had exhausted the
topic, but a few of the recent developments around the Ruby 2.7's development
lead me to revisit it.

Ruby 2.7 has become infamous at this point for several highly controversial
changes that were either quickly revised or removed. A classic tell-tale of
good project management and a clear vision for the future, right?

Let's examine a few of those ill-fated changes in no particular order...

## Flip-flops Forever

Remember how the flip-flop operator (`..`) was finally deprecated in Ruby 2.6 after
years of discussions? Well, it just got (quietly) [reinstated](https://bugs.ruby-lang.org/issues/5400#note-25).

I find it really amusing how this went down - 3 (!!!) people complained about the deprecation and Matz quickly responded with:

> I hear the negative feedback from the community. OK, I give up.
> The warning should be removed.
>
> -- Matz

There are numerous other instances where he ignored the complaints of way more people. Go figure.
As usual, there was no real discussion on the ticket - just a decision. Not to mention that
no one even thought to try to solicit more community feedback on the subject. I learned about the
removal of the deprecation when someone posted this on Twitter...

None of this is new, in Ruby-land it's business as usual. It's just becoming progressively more
frustrating for me.

## Numbered Parameters Revisited

Remember the highly controversial numbered block parameter syntax?

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

After [quite the backlash](https://bugs.ruby-lang.org/issues/15723) this syntax was revised to something more reasonable:

``` ruby
[1, 2, 3].each { puts _1 }
(1..10).map { _1 * 3 }
(1..9).each_slice(3).map { _1 + _2 + _3 }
```

A great success, right? Matz made a couple of interesting comments on this mega-thread. My favourite one was:

> Yes, I admit { @1[@2] = "Go Fish: #{@2}" } is cryptic. But {@1 * @2} is not. So use numbered parameters with care (just like other features in Ruby). The possibility to make code cryptic itself should not be the reason to withdraw a feature.
>
> The problem is introducing it or this could break existing code. That is the problem. Numbered parameters are a compromise to simplify block expression without breaking compatibility.
>
> FYI, I am not fully satisfied with the beauty of the code with numbered parameters, so I call it a compromise.
>
> -- Matz

So, he accepted something that he wasn't perfectly happy about. OK, I can
understand the need for compromises from time to time. But I totally hate this
line of reasoning "The possibility to make code cryptic itself should not be the
reason to withdraw a feature.".  I think that adding features for the sake of
adding them is not a reason to add something either.

I also think that we should be
careful with the impact of features on tools and with all the accidental
complexity they bring (e.g. now Ruby parsers are more complex).  Not to mention the
fact that no one could make a strong case for introducing this feature in the
first place. I'm still not sure what's the grand problem it solves...

## The Totally Useless Pipeline Operator

This was probably the most ridiculous part of Ruby 2.7's development cycle. At some point
the pipeline operator landed in `master`:

``` ruby
# now
foo()
  .bar(1, 2)
  .display

# Ruby 2.7
foo()
  |> bar 1, 2
  |> display
```

Everyone who has actually used the pipeline operator in Elixir (the source of inspiration for this change),
however, knows that it behaves nothing like this. Here Ruby's team basically came up with different syntax
for `.` (method invocation) with a lower precedence that allowed you to do questionable things like:

``` ruby
(a..b).each {}

a..b |> each {}
```

I won't go into much details about this misguided operator. After all, a lot of great articles exist about
it already.[^1] For me the important things here are:

* This feature was not well thought-out
* It generated a massive backlash
* It was quickly reverted

Sounds familiar, doesn't it?

## The Rise and Fall of the Method Reference Operator

Ruby 2.7 was supposed to introduce a method reference operator:

``` ruby
[2, 4, 8, 16, 32].map { |n| Math.log2(n) }

[2, 4, 8, 16, 32].map(&Math.:log2)
```

Well, not anymore. This feature was recently [reverted](https://bugs.ruby-lang.org/issues/16275).
There were some good reasons for it to be reverted, which serves to underline my point that
a lot of changes to Ruby recently were made without thinking much about their implications.
Matz, made one particularly interesting comment on this ticket:

> In recent years, we have added/discussed a lot of features inspired by functional programming. But those features are not designed with a grand design. Those are rather ad hoc additions. We felt the future, more functional Ruby should be designed with the big picture of functional Ruby programming, which we lack currently.
>
> Besides that, I don't like the specific operator (.:) which looks like braille.
>
> So you don't have to consider this revert as a rejection of this whole idea, but an invitation to the next try.
>
> -- Matz

I was really glad to see this, as it seems that Matz also understands that without a solid vision in the end
we'll just get one big mess. I'm a bit sad about the `.:` operator being reverted, as this was a feature I could
see myself using, but that's a small price to pay for a better future of Ruby.

## Parameter Forwarding Weirdness

Just when I had some hope about the future I came across another "brilliant" addition to Ruby 2.7 - parameter forwarding.
It's best explained with an example:

``` ruby
def foo(...)
  bar(...)
end
```

This defines a method `foo`, that can have any parameters whatsoever and simply
forwards all of them to `bar`. I don't know about you, but I always wanted
something like this![^2]

There are some plans to make it a bit more generic and support forwarding a
subset of all parameters:

``` ruby
def foo(a, b, ...)
  if a.even?
    bar(b, ...)
  end
end
```

Still, I think this is another random addition that doesn't solve real problems
and just adds mental and technical complexity.

## Epilogue

So, we made it to the end of this somewhat bitter and rantish article. So, what's the takeaway from
all of this, if any?

It's good that Ruby's team listens to user feedback, but it might also be nice
for them to actually start asking about feedback in some more open/structured
manner prior to making language changes.  Especially given their recent track
record with the Ruby community...  You can't really expect everyone to be
monitoring all tickets in Ruby's issue tracker, right?[^3] I've often dreamt of
a state where Ruby's team actually announces in advance the focus for the next
release (a.k.a. a roadmap), so interested parties can actually go and help with
the tasks at hand. That should not require that much extra work - just a bit of
good old planning. Some voting system on tickets and annual surveys might be a good idea as well - other programming communities are making good use of those.

Many people are excited about
Ruby 3.0, but I'm not. It's a major release in theory, but in practice it will
add relatively little to what we have today. It's not even clear if the new
concurrent programming model will make it there or not at this point.

Ruby 2.7 has showed better than ever the chaos in Ruby's development and the
lack of a clear direction/destination.  There are some signs that Matz understands this as
well, so I'm still optimistic about the future.

I think Matz should take a page from Rich Hickey's[^4] book and take a bit of [hammock-time](https://www.youtube.com/watch?v=f84n5oFoZBc) to figure
out where he wants to take Ruby next. So, Ruby, where do we go now?

[^1]: That's my favourite one <https://dev.to/baweaver/ruby-2-7-the-pipeline-operator-1b2d>.
[^2]: You can't here my voice, but that's sarcasm here.
[^3]: Especially given the fact that the vast majority of ideas for language changes are just ignored and stay dormant for ages.
[^4]: The author of Clojure.
