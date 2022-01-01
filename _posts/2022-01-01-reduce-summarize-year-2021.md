---
layout: post
title: "(reduce summarize year-2021)"
date: 2022-01-01 12:56 +0200
tags:
- Meta
- RuboCop
- CIDER
- Emacs
---

Last year was pretty bad for everyone. So bad, that I didn't really feel like writing the traditional summary article about it.
2021 wasn't exactly great either, but it was a relatively good year for me all things considered.

Below I'll quickly go over the highlights of the year for my OSS projects. I wrote a separate blog post about everything else
[here](https://batsov.com/articles/2021/12/31/2021-the-year-in-review/).

## RuboCop

RuboCop had a pretty good year. We've started the year at version 1.7 and we've had a total of 30 releases in 2021!

Nothing really ground-breaking happened - we kept fixing bugs, adding new cops and improving the existing ones. _Stability_ was
the key word this year and we've been very committed to it. There are no grand features on the horizon and our focus is unlikely to
change in the foreseeable future.

One item that I want us to tackle in 2022 is performance (improvements). Perhaps we need an ambitious goal like
"RuboCop 2x2"? Make RuboCop 2.0 2 times faster than RuboCop 1.x!

## nREPL

I'm really glad that we managed to release [nREPL 0.9]({% post_url
2021-12-12-nrepl-0-9 %}) a few weeks ago. Now, I guess the focus will
become solidifying the public API (mostly that of the built-in ops), improving
the end-user documentation and cutting the long-overdue nREPL 1.0 release.

nREPL is definitely at a good place and I think that by now we've achieved all the goals that we set out to achieve
when we moved the project out of Clojure Contrib in early 2018.[^1] Frankly speaking, we've already exceeded those original goals. A big thanks to everyone who was a part of this amazing journey!

At some point we'll have to document the nREPL protocol better as well, as it's currently easy to confuse the protocol with the reference Clojure implementation. Perhaps 2022 will be the year of awesome nREPL documentation?

## CIDER's Orchard

CIDER had a couple of solid releases this year - 1.1 and 1.2. I'm particularly proud of all the work that went into [CIDER 1.2]({% post_url 2021-12-22-cider-1-2-nice %}) and I consider it
the most important CIDER release in recent years.

Some other highlights from the Orchard:

- The new library [enrich-classpath](https://github.com/clojure-emacs/enrich-classpath) made it very easy to work with Java sources and Javadoc. It currently supports only Leiningen, but I hope this will change in 2022.
- We finally dropped `dynapath` from Orchard 0.8, which means that Orchard is now dep-free!
- `cider-nrepl` got a few small releases throughout the year (mostly bug-fixes)
- `clj-refactor.el` and `refactor-nrepl` got a ton of love (and releases) in 2021 - I think they saw more activity in 2021 than in the past 5 years combined!

Most of the Clojure Emacs packages are now available for installation via [NonGNU ELPA](https://elpa.nongnu.org/nongnu/). That's as close as we can get
to out-of-the-box support for Clojure in Emacs, as this repository is enabled by default in the upcoming Emacs 28. This mean you can just do something like
`M-x package-install cider` without any additional setup.

The only kind of forgotten package in 2021 was `sayid`, but I hope that this is going to change in 2022.

## Everything Else

Sadly, I didn't have much time for all my other projects. I really wanted to work a bit more on Projectile and Prelude, but alas... At least
things there are always moving forward, albeit slowly. By the way, did I mention that most of my Emacs projects (not just the ones related to Clojure) are now available on Emacs's new
built-in repository NonGNU ELPA? Projectile, crux, super-save, etc - you'll find them all there.

All the style guides had a quiet year as well and that's fine. It's not like the best practices change every day. Still, all of them could use more copy editing,
clear rationale and more/better examples. No rush, though.

## Discord

As outlined [here]({% post_url 2021-10-23-using-discord-for-oss-projects %}) I've been trying to use Discord as a support channel for my OSS projects. So far the results have been a bit underwhelming,
but I'm still optimistic that Discord can be a valuable tool for OSS communities.

CIDER is probably the only "success story" to date (with about 200 people in the CIDER Discord server), but this still pales to the number of people in the Clojurians Slack.

## Funding

The donations I've been receiving for my OSS projects have been more or less steady for the past 3 years (e.g. CIDER has pretty much the same donations on OpenCollective today as it had in early 2019). OpenCollective and GitHub Sponsors gave some initial boost to donations in
2018-2019, but there was definitely no steady uptrend in the donations in the years since.

Of course, from time to time there are notable exceptions like:

- Cognitect donating $500/month for my work on CIDER & friends
- Clojurists Together's annual funding of $1500/month for CIDER
- Occasional bigger one-time donation

Still, the dream of working full-time on OSS projects down the road seems to be as elusive as always. From time to time I come across idealistic articles like
[Funding isn't the Problem with Open-Source](https://www.haskellforall.com/2021/12/funding-isnt-problem-with-open-source.html), and I certainly can't agree with them.
For one, I've yet to encounter those evil corporations that are willing to pay me money to subvert my projects to their bidding (and evil ways, of course). Topic
for some dedicated article I guess.

I admire a lot people like [Michiel Borkent](https://www.michielborkent.nl/), who recently went all-in on his OSS work. They give me hope that everything is possible if you put your mind to it. Perhaps I'll be ready to take the red pill a few years down the line myself. Especially if I make the right crypto "investments" in the mean time.[^2]

## Changes to Meta Redux

You might have noticed that this year I've been writing fewer articles here and they are mostly about my OSS projects. That's related my
decision to revive my original site [(think)](https://batsov.com) and do more of my writing there. And give some clear focus to Meta Redux by doing son.

## Epilogue

2021 marked the 10-year anniversaries of my oldest projects Projectile and Emacs Prelude. A decade working on OSS projects! Wow, time is certainly not
on my side, as I have ideas for at least 50 years of work...

I don't have any grand plans for 2022 right now. Clearly my focus will stay with CIDER and RuboCop, but I hope that I'll manage to find some time for my smaller projects as well. One thing is certain, though - I love hacking on OSS projects just as much as I always did and I'm always looking for ways to take things to the next level. Happy New Year! Keep hacking!

[^1]: <https://github.com/nrepl/nrepl/issues/1>
[^2]: Just kidding. I hate crypto, but many of my friends seem to believe that's the quickest way to wealth and prosperity.
