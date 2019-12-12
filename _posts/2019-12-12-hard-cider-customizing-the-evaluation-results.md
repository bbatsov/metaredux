---
layout: post
title: 'Hard CIDER: Customizing the Evaluation Results'
date: 2019-12-12 00:18 +0200
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

Yesterday someone asked on the Clojurians Slack if you could configure CIDER to
display interactive evaluation results (results originating from source buffers most of the time)
only in overlays (inline), as by default they are displayed both in the Emacs minibuffer and inline.
Well, I've often said that in CIDER almost everything is configurable and so is this:

``` emacs-lisp
(setq cider-use-overlays t)
```

The default value of this configuration variable is `'both`. You can also easily disable the inline results
by setting it to `nil`.[^1]

<!--more-->

But wait, there's more! Did you know that a few other aspects of interactive evaluation results are configurable?
For instance, you can control whether results are font-locked (syntax highlighted) as Clojure code or not with the configuration
variables `cider-result-use-clojure-font-lock` (for the minibuffer) and `cider-overlays-use-font-lock` (for inline results).
Both are enabled by default. If you opt to disable Clojure font-locking for inline results they will the `cider-result-overlay-face`
instead. This might make it easier for you discern your results from the result of your code.

Inline results are normally displayed at the end of the current line, but you can also display them at point:[^2]

``` emacs-lisp
(setq cider-result-overlay-position 'at-point)
```

You can control the prefix used with results:

``` emacs-lisp
(setq cider-eval-result-prefix "--> ") ; it's => by default

;; or you can remove it completely
(setq cider-eval-result-prefix "")
```

Historically tools like SLIME didn't use any prefix before the results, but I've tweaked this as it seemed more in
line with what most other tools are doing.

Another thing that you can control is when inline results would disappear. Normally this happens after your next action[^3], but you can
keep them around for some time limit (in seconds):

``` emacs-lisp
;; keep inline results around for 5 seconds
(setq cider-eval-result-duration 5)

;; or forever
(setq cider-eval-result-duration nil)
```

Crazy, right? Who needs so many configuration options? Well, you know how I keep repeating that Emacs is all about
making it your own and customizing to your way of work and your preferences. CIDER just embraces the Emacs
philosophy to the limit.

I agree that many of those configuration options are somewhat confusingly named and therefore a bit hard to find.[^4]
This is something that we'll have to address down the road. Some of them are documented in the manual, but we can
certainly do better.

That's all I have for you today! See you tomorrow!

[^1]: This used to be the original behaviour in CIDER, because we introduced the concept of inline results a few years ago.
[^2]: This was introduced in CIDER 0.23.
[^3]: Technically speaking, after you invoke another Emacs interactive command.
[^4]: Especially for people not familiar with Emacs terminology.
