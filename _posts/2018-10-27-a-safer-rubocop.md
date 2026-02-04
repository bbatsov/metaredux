---
layout: post
title: "A Safer RuboCop"
date: 2018-10-27 09:11:15 +0300
categories: posts
tags:
- Ruby
- RuboCop
---

I'll assume that if you're reading this post, then you probably already know what [RuboCop][] is, so I won't go into
any details on that subject.[^1]

Yesterday the project reached a very important milestone[^2] on its way to the "magic" 1.0 release - namely the introduction
of extended cop[^3] metadata and the ability to run only cops and auto-corrections that can't possibly break your code by
changing its meaning. We call such cops and their auto-corrections "safe". Making use of the new functionality is very easy:

``` console
# Run only safe cops.
$ rubocop --safe

# Perform only safe auto-corrections.
$ rubocop --safe-auto-correct

# Run only safe cops and perform only safe auto-corrections.
$ rubocop --safe --safe-auto-correct
```

<!--more-->

Probably some of you're wondering what will happen if you run all cops, but you perform only safe auto-corrections, right?
Well, you'll get all possible offenses reported, but RuboCop is going to fix automatically only the ones that
are safe to fix.

Here's also a small tip for you - if you want to always run only safe cops and do safe auto-corrections you can make use
of the handy `.rubocop` file. Just create a `.rubocop` file in the root of your project and place there the following:

```
--safe --safe-autocorrect
```

By the way, did you know that you could also pass some default command-line options to RuboCop via the `RUBOCOP_OPTS` environment variable?
It takes precedence over options set via `.rubocop`.

If you're in a hurry you can safely stop reading at this point. For those of you who'd like to learn a bit more about
all this, however, I've got a few extra things I'd like to share.

## Diving Deeper

So, what makes a cop unsafe anyways? And why does RuboCop even have cops that we know can create trouble? Well, this
is closely tied with Ruby's dynamic nature and our inability to reliably determine things like the type of some
receiver or what libraries are loaded at any given time when doing static analysis. Here are a few classic examples:

``` ruby
# Is something an instance of Enumerable or something else?
something.find_all { ... }

# Is something a hash?
something.keys.include?(key)
```

In the first example normally `Style/CollectionMethods` would suggest going for `select` instead of
`find_all`, but there's also the chance that the receiver (`something`) is not really an instance of a
collection class that implements `Enumerable`.

In the second example the `Performance/InefficientHashSearch` would normally suggest using `something.key?(key)`,
which would usually be what you want to do, but in case you have some other classes with `keys` instance methods
you'll get false positives for them. These type checks are pretty useful in general (and that's why they exist), but
they can also wreak havoc from time to time (especially if you don't have a good test suite).

An even more nuanced example would be:

``` ruby
# Is something an instance of class that implements empty?
# Does it have a size alias for length?
something.length == 0
```

`Style/ZeroLengthPredicate` would suggest using `empty?` here and most of the time that'd be a great idea, but not
every class that has `length` (or `size`) method would define a matching `empty?` method. Here's a very real example where this check
would fail:

``` ruby
File.stat(manifest_file).size == 0
```

Clearly here for the `stat` object the notion of being empty doesn't make sense (although it does make sense for the file itself).
RuboCop, however, can't really tell what's a collection and what's something different, because it has no type information.
Here we can do some special handling if the receiver is `File.stat`, but that's just patching one specific problem and
not a real solution.
Another similar example would be the usage of `size` vs `length` - most often classes define those methods as aliases and people
prefer to stick one or the other, but that's also not always the case.[^4]

When it comes to unsafe auto-corrections I think the classic examples would be something like:

``` ruby
# It'd be nice to use $CHILD_STATUS here instead, but is the English library loaded?
if $? == 0
  # ...
end

# x.nonzero? might read better, but it has different semantics.
result = !x.zero?
```

Here in the first example `Style/SpecialGlobalVars` would suggest by default to prefer names from the `English`
library that's bundled with Ruby, but it's not loaded by default. As Ruby doesn't force you to load library
deps explicitly in the beginning of each source file go figure if this library is loaded or not, so you can safely
replace the cryptic Perl-inspired name, which something more sensible. This also highlights an important RuboCop
limitation - it operates (almost all of the time) on a single file and has no extra context about the other files
that you might have in your project and the dependencies between them.

The second example is more nuanced - Ruby's `Integer` class has both a `zero?` and `nonzero?` method and your
first instinct would be that it's probably safe to replace `!zero?` with `nonzero?`, but unfortunately they have different semantics.

``` ruby
# A classic Ruby WAT :-)
0.zero? # => true
1.zero? # => false
0.nonzero? # => nil
1.nonzero? # => 1
```

Pretty crazy, right?[^5]

In practical terms this means that depending on how the result of the method was used things will be business as usual
or breakage to your code (the need for a "real" boolean value vs the need for something truthy or falsy).

Here's another classic example:

``` ruby
for name in names do
  # ...
end
```

Normally we'd want to replace this with `Enumerable#each`, because it's more idiomatic and variables defined in the body
of `for` leak outside of it (there's no proper block scope there), but because of this leakage of scope it's extra hard
to be 100% something won't break if you replace `for` with `each`. You actually have to check if the body of the `for`
establishes some bindings and afterwards you have to check if some of them are not actually used outside of it.
That's perfectly doable, but it's going to complicate a lot the code and we haven't really done it.

How does RuboCop know which cops and auto-corrections are "safe"? Well, it's very simply - everything's considered
safe by default unless it's marked with some extra metadata in the cop's definition. If we're not confident that
a cop won't produce any false positives we marked with `Safe: false` and if a cop's auto-correction doesn't produce
an equivalent transformation of the code we mark with `SafeAutoCorrect: false`. It's pretty simple, right?

Here's the metadata for `Style/CollectionMethods`:

``` yaml
# A completely unsafe cop
Style/CollectionMethods:
  Description: 'Preferred collection methods.'
  StyleGuide: '#map-find-select-reduce-size'
  Enabled: false
  VersionAdded: 0.9
  VersionChanged: 0.27
  Safe: false

# A cop which produces reliable offenses (warnings), but has unsafe auto-correction
Style/SpecialGlobalVars:
  Description: 'Avoid Perl-style global variables.'
  StyleGuide: '#no-cryptic-perlisms'
  Enabled: true
  VersionAdded: 0.13
  VersionChanged: 0.36
  SafeAutoCorrect: false
```

If a cop is marked as unsafe then it's auto-correction automatically becomes unsafe as well.
As you can notice in this example historically we dealt with the problem differently - we marked cops
that were likely to generate many false positives[^6] as disabled. We also marked some problematic
auto-corrections as disabled. The value of the new attributes is that now you have even more
granularity over what exactly you're running and it's also immediately apparent whether some cop
can introduce any semantic changes to your code or not. It might not sound like a big deal, but I think that's super
important.

You might also notice that as part of this task we've also added some historical data for all of our cops.
`VersionAdded` denotes when a cop was introduced and `VersionChanged` denotes when a cop's public behaviour
changed most recently (e.g. its default configuration was updated).

The real work on this issue was mostly going over all the cops and annotating the unsafe cops accordingly.
You can read more about underlying metadata [here](https://github.com/rubocop-hq/rubocop/issues/5978) and
[here](https://github.com/rubocop-hq/rubocop/pull/6293).

I'd like to insert here a very big "Thanks!" to [Maxim Krizhanovsky](https://github.com/Darhazer) who
did all the heavy lifting!

## Epilogue

They say the most important step a person can take is the next step and I think that's true.

The next big step in making RuboCop even safer is going to be to stop enabling new cops out of the box (unless that's what a user wants).
Take a look at [the ticket](https://github.com/rubocop-hq/rubocop/issues/5979) that's (supposedly) going to be the end of painful RuboCop upgrades.
We're still looking for a volunteer to tackle this, so this might be your opportunity to help the Ruby community!

I hope this post was useful and enjoyable to you!

[RuboCop]: http://rubocop.org
[^1]: For those of you who don't know - it's a popular Ruby static code analyzer and formatter.
[^2]: RuboCop 0.60. The complete release notes are [here](https://github.com/rubocop-hq/rubocop/releases/tag/v0.60.0).
[^3]: RuboCop's lingo for a check.
[^4]: And as Jeremy points out [here](https://bugs.ruby-lang.org/issues/14136#note-9) `size` can be used very differently, compared to `length`, in certain context (e.g. `house.size`).
[^5]: There's actually [quite the story](https://bugs.ruby-lang.org/issues/9123#note-14) behind this.
[^6]: Or a lot of controversy.
