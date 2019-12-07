---
layout: post
title: Dead Simple Emacs Screencasts
date: 2019-12-07 12:37 +0200
categories: posts
tags:
- Emacs
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

Finally on the 7th day of the Advent we made it to the One True Editor!
Today's topic is doing screencasts with Emacs the right/simplest way.

Generally when you're doing screencasts of a text editor session the most
important thing is to make it clear what keys are you pressing and what commands
are being invoked by those keys (as you might have non-standard keybindings).
Traditionally people approach this problem by installing some additional program
that displays on screen all the keystrokes alongside their editor. I'm not sure
what most people are using, but I'm quite fond of the open-source tool
[keycastr](https://github.com/keycastr/keycastr). That's a perfectly fine
solution for most editors, but it has two crucial shortcomings:

* It's not written in Emacs Lisp
* It will show you only the keystrokes, as it doesn't know anything about the underlying editor commands

I guess in some editor communities those shortcomings could be overlooked, but
this is Emacs!  Enter the Emacs package
[keycast](https://github.com/tarsius/keycast), the hero of today's
article. After installing it, all we need to do is enable `keycast-mode` and
some magic happens (pay attention to the modeline):

![keycast_demo.gif](/assets/images/keycast_demo.gif)

Pretty cool, right? That's just 250 lines of Emacs Lisp code, and it's a perfect illustration of might
and magic of Emacs!

When you're done with your screencast just do `M-x keycast-mode` again to disable `keycast`.
As you might expect from an Emacs package, `keycast` is highly configurable. I'm too lazy today to go into any details
there, but everything you need to know is just `M-x custimize-group keycast` away.

Kudos to the legendary Emacs hacker [Jonas Bernoulli (a.k.a. `tarsius`)](https://github.com/tarsius/keycast) for
creating another awesome package. By the way, if you love `magit` and everything else Jonas is doing for
the Emacs community - please consider [supporting his work](https://github.com/sponsors/tarsius).

That's all I have for you today! Keep screencasting!
