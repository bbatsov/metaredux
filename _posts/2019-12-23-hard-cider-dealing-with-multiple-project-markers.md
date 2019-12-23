---
layout: post
title: 'Hard CIDER: Dealing with Multiple Project Markers'
date: 2019-12-23 10:02 +0200
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

A while ago I wrote [an article explaining how the jack-in process in CIDER works]({% post_url 2019-11-02-hard-cider-understanding-the-jack-in-process %}), but I omitted one subject -
what happens if your project has multiple project marker files (e.g. `project.clj` and `deps.edn`).
This wasn't a big issue a few years back, when almost everyone was using Leiningen, but now it's pretty
common to have some project with both Leiningen and `tools.deps` project marker files in it, or maybe `tools.deps`
and `shadow-cljs`. So, how does CIDER handle such ambiguity and decide which tool to use when starting a REPL?

Well, by default it will simply prompt you to select one of the build tools it detected. Pretty simple, but
also a bit annoying if you keep selecting the same tool all of the time. Fortunately, CIDER has an option
to customize this though the `cider-preferred-build-tool` configuration variable.[^1] If you're fond of Leiningen you can
instruct CIDER to always prefer it like this:

``` emacs-lisp
(setq cider-preferred-build-tool 'lein)
```

In practice it will be common to have the preferred build tool depending on the project you're working on,
so an optimal approach to leverage this variable would be through [project-specific configuration]({% post_url 2019-10-05-hard-cider-project-specific-configuration %}):

``` emacs-lisp
((clojure-mode
  (cider-preferred-build-tool . lein)))
```

In the end of the day you might combine the two approaches by putting in your global configuration (e.g. `init.el`) your
favourite tool and overriding this default choice on a per-project basis via `.dir-locals.el`. That's what I normally do,
although frankly speaking, I restart my REPLs so rarely that I don't have any issues being prompted about the
build tool to use every time I do so.

That's all I have for you today. See you again tomorrow!

[^1]: CIDER uses the "build tool" terminology for historical reasons, as `tools.deps` didn't exist back then.
