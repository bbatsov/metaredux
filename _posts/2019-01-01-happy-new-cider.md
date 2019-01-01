---
layout: post
title: Happy New CIDER!
date: 2019-01-01 15:49 +0200
category: posts
tags:
- CIDER
- Clojure
- Emacs
---

What can be a better start of 2019 than some freshly brewed CIDER'(0.)19?
Consider the release of the latest CIDER my attempt to keep the New Year festivities
going for a while longer. It's also an attempt to clear some overdue items from
my (humongous) personal todo list at the start of the new year (other overdue items I tackled
today were shaving and taking a shower).

Anyways, the most important thing is that CIDER 0.19 (Raleigh) is here! Its codename is
a tribute to the wonderful time I spent in Raleigh, NC with my buddy Jin around
Clojure/conj (Raleigh is right next to Durham, where the conference took place).
Raleigh is not a big city and it's also not a big CIDER release - 0.19 is mostly polishing
work for the much bigger 0.18. In particular we focused a lot on improving the new
connection management subsystem.[^1] We've also introduced the concept of [friendly sessions](
http://www.cider.mx/en/latest/managing_connections/#friendly-sessions), which should
solve the commonly reported problem that moving from a project to the source of its dependencies
would result into an error saying that there are no CIDER sessions associated with them.

All the problems that were reported around the new connection/session
management reminded me once again how hard it is for a tool writer to
guess how people are actually using their tool.  For instance - I
never edit the code of dependencies inline, so I completely ignored
this use-case while we were revamping the connection management with
Vitalie. It was quite enlightening for me to discuss with our users
their workflows over the past couple of months and how we can make
things better for them. That also reminded me for the millionth time that I should probably
document somewhere the "intended" workflow, so at least people would know
how certain features were envisioned to be used.

One more thing about CIDER 0.19 - it fixes [an annoying problem](https://github.com/clojure-emacs/cider-nrepl/issues/549),
related to the changed format of the error messages in Clojure 1.10.

You can see the complete list of changes in CIDER 0.19 [here](https://github.com/clojure-emacs/cider/releases/tag/v0.19.0).

What's next for CIDER in 2019? Short-term the focus is going to be
more fixes and more polish, and afterwards we'll see. Now that latest
Leiningen (2.8.3) bundles the modern [nREPL](https://nrepl.org), CIDER
is going to drop support for `tools.nrepl` and implement all the cool
new features provided by recent nREPLs (e.g. native pretty-printing).
Everyone will do well to upgrade to Leiningen 2.8.3 as soon as
possible to avoid unexpected errors with CIDER 0.20.

As usual, people can refer to CIDER's
[roadmap](https://github.com/clojure-emacs/cider/blob/master/ROADMAP.md)
if they are curious about the high-level plans for the future or they
are looking for ideas as to how to contribute to the project.
One thing that has been on the back of my mind recently is that we should
also start evaluating the current set of features and customization
settings and start removing stuff that's rarely used for the sake of
having a more compact and easier to maintain codebase.

While on the subject of contributing to the project - a few people
asked me at Clojure/conj and ClojureX how they can donate money to
CIDER - all the donation channels are documented
[here](http://www.cider.mx/en/latest/about/contributing/#funding),
with OpenCollective currently being the preferred channel. As usual
the year begins with the dream that we're going to get enough funding
from users and sponsors to be able to allocate more time on the project,
pay bounties for certain features to be developed and so on. So far
donations haven't worked out well for CIDER, but at least their annual
amount has been gradually climbing (it reached almost $5000 this year).

Some people suggested recently that CIDER should start charging
commercial users, as most companies would never really donate money,
but would be perfectly fine to pay an annual license of $50-60. I'll
try to explore this avenue further, although I'm definitely not
certain about the best way to approach this. Probably it's as easy as
what Cognitect did for REBL (adding some text to the license that
commercial users should pay something like $5/month via
OpenCollective), but I'll have to find some time to research this
further. If someone has ideas/experience in this area I'd love to hear
them!

You can see a full list of the changes in CIDER 0.19 [here](https://github.com/clojure-emacs/cider/blob/master/ROADMAP.md).
Thanks to all the amazing contributors who worked on this release! You rock and I love you!

That's all from me for now. Enjoy the new CIDER (ir)responsibly and have a wonderful new year full of
parentheses, sweet REPLs, love and happiness!

[^1]: https://github.com/vspinu/sesman
