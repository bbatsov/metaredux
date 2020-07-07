---
layout: post
title: A Safer RuboCop, Part Deux
date: 2020-07-07 10:48 +0300
tags:
- Ruby
- RuboCop
---

A while ago I wrote [an article]({% post_url 2018-10-27-a-safer-rubocop %}) about our recent efforts to make RuboCop safer
in terms of the code changes that it suggests and performs in auto-correct mode.
Today I'll follow up with a small update.

In preparation for the long-awaited RuboCop 1.0, RuboCop's team spent a lot of time
recently adding auto-correction to more cops, polishing the internal auto-correction logic,
and marking cops with unsafe auto-correction accordingly. Recently we reached another
important milestone in our quest for safety - we've made safe auto-correct the default behavior
of RuboCop! The change essentially makes `--auto-correct` and `--safe-auto-correct` the same.[^1]

``` shellsession
$ rubocop -a
# or
$ rubocop --auto-correct
# or
$ rubocop --safe-auto-correct # deprecated
```

I guess you might be wondering can you still have RuboCop run every possible auto-correction.
Yes, you can!

``` shellsession
$ rubocop -A
# or
$ rubocop --auto-correct-all
```

I think that's pretty simple - a bigger A means more epic (and more dangerous) auto-corrections. :-)
[RuboCop 0.87](https://github.com/rubocop-hq/rubocop/releases/tag/v0.87.0) was released yesterday with these changes. I hope you'll enjoy them!

In general I've always felt it's a bad idea to run all the auto-corrections possible, especially on a big codebase.
Likely you'll immediately notice some problems, but you might have trouble finding where did they come from and which
correction caused them. That's why my recipe for (unsafe) auto-corrections has always been less ambitious:

``` shellsession
# Run auto-correction for a cop at a time
$ rubocop --auto-correct --only Some/Cop
# Review carefully the diff
# Run the specs/unit tests
$ rspec
# All is green. Let's ship it!
# Moving forward with the next cop...
```

I encourage more people to operate in this incremental manner, as it makes it much easier to deal with unsafe corrections, which
despite their name work pretty well most of the time, but still require a bit of supervision from a human.

That's all I have for you today. Keep hacking!

[^1]: `safe-auto-correct` is now deprecated and will eventually be removed.
