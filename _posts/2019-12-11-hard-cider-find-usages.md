---
layout: post
title: 'Hard CIDER: Find Usages'
date: 2019-12-11 13:00 +0200
categories: posts
tags:
- Clojure
- Emacs
- CIDER
- Series
- Hard CIDER
- Meta Advent 2019
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

The [State of CIDER survey]({% post_url 2019-11-02-state-of-cider %}) was super insightful for me on many levels.
Here's one example - I realized that users might have missed some recent developments. A few people had commented it'd be
nice if CIDER had a build-in "find usages (references)" functionality and funny enough - it already has this.

<!--more-->

The functionality is based on [ideas I've shared in the past]({% post_url
2019-05-04-discovering-runtime-function-references-in-clojure %}) and was
introduced in CIDER 0.22. I guess for the majority of people the most interesting commands
would be `cider-xref-fn-refs` (`C-c C-? r`) and `cider-xref-fn-refs-select`
(`C-c C-? C-r`).[^1] The first command will show the usages of the function at point in a dedicated buffer
and the second will show them in the minibuffer.[^2] Here's how they look in action:

![cider_find_usages.gif](/assets/images/cider_find_usages.gif)

Keep in mind the following limitations:

- This works only for Clojure
- It's powered by runtime state analysis, which means it will show only data for loaded namespaces
- It doesn't (currently) find usages in lambdas
- It doesn't give us the precise locations where something is used, we only know that it's used

On the bright side:

- It's super fast
- It doesn't require any static code analysis
- It's still more reliable than `grep`

The functionality is not perfect, but at least it's there if you need it. As a bonus you get a quick way to navigate to
all of the functions used by some function using `cider-xref-fn-deps` (`C-c C-? d`) and `cider-xref-fn-deps-select` (`C-c C-? C-d`).
Those are pretty handy if you don't want to jump to the source of some function to see what it refers to internally.

Don't forget you also have a couple of third-party alternative:

- The much more sophisticated AST-powered "find usages" provided by `clj-refactor.el`
- [Projectile](https://github.com/bbatsov/projectile)'s "grep in project" (`projectile-grep`, typically bound to `C-c p g`)

That's all I have for you today! See you tomorrow!

[^1]: I know those keybindings kind of suck because of `C-?`, but we ran out of good keybinding options a while ago.
[^2]: If you're familiar with `cider-apropos` you'll notice the UI is very similar.
