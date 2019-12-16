---
layout: post
title: Painless RuboCop Upgrades with Mry
date: 2019-12-16 15:19 +0100
categories: posts
tags:
- Ruby
- RuboCop
- Meta Advent 2019
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

Yesterday [I wrote about some recent work in RuboCop that resulted in a bunch of
breaking changes]({% post_url 2019-12-15-a-uniform-rubocop %}). Today I'll share
with you a simple strategy for dealing with such changes easily.

Here are the top two problems that people experience on RuboCop upgrades:

* New offences coming from newly created cops
* Breaking changes to the configuration (e.g. cops or cop parameter were renamed)

While RuboCop itself mitigates the second problem to some extent (via some
deprecation messages) you still have to update your configuration manually,
which kind of sucks.
Enter [mry](https://github.com/pocke/mry) - a cool little tool by RuboCop Core
Team member [Masataka "Pocke" Kuwabara](https://github.com/pocke). In case
you're wondering - mry stands for "Migrate RuboCop.Yml".[^1]
Using the tool is super simple:

``` shellsession
$ mry .rubocop.yml
```

That's going to apply all the necessary changes for the latest RuboCop release.
You can also be a bit more specific about the target RuboCop version:

``` shellsession
$ mry --target=0.75.0 .rubocop.yml
```

That's obviously quite useful if you're not targeting the latest and greatest RuboCop for some reason.
Let's see `mry` in action now:

![mry_demo.gif](https://cloud.githubusercontent.com/assets/4361134/25322816/8188f21a-28f5-11e7-9915-93f72034e3ea.gif)

Pretty neat, right?

I guess one obvious question that you might have is why isn't this in RuboCop itself?
Frankly, I don't have a good answer. We might incorporate the `mry` functionality down the road,
but we're also planning to really reduce this type of changes after RuboCop 1.0 is out.
Anyways, it'd be nice if this was some generic framework in RuboCop that was accessible to all
plugins, as currently `mry` tracks only changes in the main `rubocop` gem.

That's all I had for you today. See you again tomorrow!

[^1]: Naming is hard.
