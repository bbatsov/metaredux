---
layout: post
title: 'Hard CIDER: Hard Restart'
categories: posts
tags:
- Clojure
- Emacs
- CIDER
- Series
- Hard CIDER
---

Another day, another hard topic to tackle! How do you (hard) restart CIDER?
That should be simple, right? Well, it really depends on your perspective
and on your definition of a "restart".

CIDER has two commands with the word "restart" in their name - one is
`cider-restart` and the other is `sesman-restart`. I've noticed that the majority
of CIDER's users define "restart" as:

* Restart the nREPL server
* Reconnect CIDER to the newly started nREPL server

I've also noticed the majority of the users expect the command `cider-restart` to do exactly this.
It might come as a shock to some people, but this command does something pretty different -
it simply kills the current nREPL connection (which in CIDER is the same as a REPL buffer) and
creates a new one to the same nREPL server. I like to think of this as "soft restart".
Still, the question remains - how can we do a "hard restart"?

Enter `sesman-restart`. This command is a whole different beast - it will actually restart
the underlying nREPL server and re-create all connections within the same CIDER session.
I guess that's more aligned with the expectation of most people.

The way I see it there are two primary reasons for the confusion:

* `cider-restart` used to behave differently before we introduced the concept of multiple connections grouped
in a single session (the `sesman` library provides this session abstraction)
* most people don't understand very well the concept of sessions in CIDER and the relationship between CIDER and `sesman`.
As a corollary - they won't think to look for commands that are not prefixed with `cider-`.

There's also the fact that the name `cider-restart` is probably not ideal. Perhaps a better name would be `cider-restart-repl`
or `cider-restart-connection`? Naming is hard...

I'll ruminate on the subject of what's the best course of action. As usual -
feedback and ideas from you would be welcome!  In the mean time I plan to work
on improving the [connection management
documentation](https://docs.cider.mx/cider/usage/managing_connections.html), so
it's clearer how the two command differ.

That's all I have for you today. This episode was brought to you by [Clojurists Together](https://www.clojuriststogether.org/).
They are awesome and so are all of you! Keep hacking!
