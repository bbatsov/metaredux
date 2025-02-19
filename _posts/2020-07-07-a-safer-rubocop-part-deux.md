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
Today I'll follow up with a small update on the latest developments in that area.

In preparation for the long-awaited RuboCop 1.0, RuboCop's team spent a lot of time
recently adding auto-correction to more cops, polishing the internal auto-correction logic,
and marking cops with unsafe auto-correction accordingly. Recently we reached another
important milestone in our quest for safety - we've made safe auto-correct the default behavior
of RuboCop! The change essentially makes `--auto-correct` and `--safe-auto-correct` the same.[^1]

```console
$ rubocop -a
# or
$ rubocop --auto-correct
# or
$ rubocop --safe-auto-correct # deprecated
```

I guess you might be wondering can you still have RuboCop run every possible auto-correction.
Yes, you can!

```console
$ rubocop -A
# or
$ rubocop --auto-correct-all
```

I think that's pretty simple - a bigger A means more epic (and more dangerous) auto-corrections. :-)
[RuboCop 0.87](https://github.com/rubocop-hq/rubocop/releases/tag/v0.87.0) was released yesterday with these changes. I hope you'll enjoy them!

I hope the rationale behind this is clear - a lot of people would run auto-correct in bulk and would get surprised that RuboCop made
(some) code changes that are not 100% compatible with the original code. We obviously want to limit such unpleasant surprises.
Originally I was optimistic that most people would be fine with the occasional small breakage here and there, given the overall
savings they'd get from the auto-corrections, but the feedback me and our team got over the years made me reconsider my
stance of the default behavior.[^2]

In general I've always felt it's a bad idea to run all the auto-corrections possible, especially on a big codebase.
Likely you'll immediately notice some problems, but you might have trouble finding where did they come from and which
correction caused them. That's why my recipe for (unsafe) auto-corrections has always been less ambitious:

```console
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
[^2]: I was also optimistic that most projects would have solid test coverage that makes it easy to find such breakages.
