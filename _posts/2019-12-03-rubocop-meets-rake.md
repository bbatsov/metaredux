---
layout: post
title: RuboCop Meets Rake
date: 2019-12-03 10:51 +0200
categories: posts
tags:
- Ruby
- RuboCop
- Rake
- Meta Advent 2019
- Series
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

Another day, another (official) RuboCop extension. This time it's focused on the legendary Rake.

I'm happy to announce that for a while we (RuboCop's team) have been working
on [rubocop-rake](https://github.com/rubocop-hq/rubocop-rake), which extends RuboCop with some extra knowledge of Rake.
Currently it includes checks for things like duplicated tasks and namespaces, missing task descriptions, etc.
A list of all cops is available [here](https://github.com/rubocop-hq/rubocop-rake/blob/master/config/default.yml).

Setting up `rubocop-rake` is really simple. Just add it to your `.rubocop.yml` and you're good to go:

``` yaml
require:
  - rubocop-other-extension
  - rubocop-rake
```

By default all the cops are scoped to `Rakefile` and `*.rake`. I doubt you'll ever have to change those defaults.

Rake is a simple tool and I imagine that `rubocop-rake` will always remain a small extension. Still, any help
with it would be appreciated, as there's always room for improvement.

That's all from me for now. I hope you'll enjoy `rubocop-rake`. I'm looking forward
to hearing what you think about it. Until next time! Keep hacking!
