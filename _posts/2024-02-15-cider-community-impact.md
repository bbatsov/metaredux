---
layout: post
title: 'CIDER: Community Impact'
date: 2024-02-15 16:20 +0200
tags:
- CIDER
- Clojure
- Emacs
- OpenCollective
---

> Alone we can do so little; together we can do so much.
>
> -- Helen Keller

I've started [CIDER's OpenCollective](https://opencollective.com/cider) some 7-8
years ago and over the course of time it became of the main donation channels
for the project. In good years it would raise somewhere between $10,000 and
$15,000. While my initial plan was to use the money to fund my own work on the
project, I wasn't in a rush to withdraw them as I had a pretty stable job, that
managed to cover our family's expenses. So, I felt it was better to let the money
get accumulated for a time when we could fund the work of other developers, or I
find myself out of a job and could focus my entire attention on CIDER for a few
months.[^1] One can dream, right?

Last year, one of CIDER's long-term contributors
V. (a.k.a. [vemv](https://github.com/vemv)) was between jobs and I he offered to
work on a long list of problems and improvements if we could fund his work with
money from the collective. I was excited, as with me juggling so many projects
it's hard to find enough time to go in "the zone" and work for an extended
period of time on one particular project. I've noticed that over the years my
responsibilities as maintainer shifted from "writing all (most of) the code" to
things like:

- building a long-term strategy for the projects
- evangelizing the projects (with blog posts, presentations, etc)
- providing support for the end-users
- writing user and developer documentation
- reviewing bug reports and feature requests
- "recruit" contributors
- helping contributors and reviewing their submissions

I guess you get the idea. It's not very different from the professional
transition I had in the past from a software engineer to an engineering
manager. Anyways, the role of the maintainer is a very big topic that I plan to
expand upon in the future, and I'm not going to dwell too much on it right
now. What's relevant for today's article is that the primary role of the
maintainer (at least the way I see it) is to make the right decisions for the
health and future of the project.

In this case it seemed prudent to fund the work of a trusted contributor, who
happened to have the time and the energy to work on complex bodies of work. We
started our collaboration in May 2023 and I hope you'll agree that the results
have been impressive:

- 6 CIDER feature releases (1.8-1.13)[^2]
- Massive improvements to error handling, Java documentation rendering, the indentation logic, the test runner and the data inspector
- Many (obscure) long-standing bugs were finally addressed
- Countless releases of related projects (e.g. `enrich-classpath`, `orchard` and `cider-nrepl`)
- Increased bandwidth for user support (as V. provided a lot of support himself over Slack and GitHub)

So much was done last year that I'm certainly forgetting something.
At any rate, I'm quite happy with the outcomes, and I believe they showcase
great how additional funding can help FOSS projects. The total funding our
collective contributed to V.'s work was about $65,000 and while that's a lot of
money, I think it was money well spent. I've opted to fund his work at actual
market rates, so he'd be most incentivized to deliver the best possible
outcomes. That's the only way to do OSS work sustainably.

**CIDER had a great 2023 and it had it because of its awesome community! You rock!**

Now our collective is drained and it's not clear when we'll be able to do
something like this again. If we assume a budget of $10,000/year that would
happen 7 years down the road. Sadly, with the economic downturn that affected
heavily the IT industry the amount of donations my projects have been getting
has plummeted. Lots of companies and individuals pulled out and donations across
GitHub Sponsors and OpenCollective in 2023 are down about 20% compared
to 2022. I recall there was a smaller drop from 2021 to 2022.  Sadly, donations
remain a very unstable and unpredictable source of funding.

It'd be great if we had 20+ companies supporting the project with something like
$500+/month (which is a drop in the pond for most businesses), but I doubt this
will ever happen. Donating to OSS projects is a hard sell in general, and a
plugin for Emacs is probably a sale that is as hard as it gets. Perhaps down the
road we'll try some focused fundraisers for specific features/improvements,
although I have some reservations about such an approach as well.

CIDER's success with funding some development via OpenCollective is not an
isolated case, of course. [Clojurists
Together](https://www.clojuriststogether.org/) is another great case study for
the positive impact funding can have on FOSS projects.  I guess no other
organization has done more for the Clojure community, and I'm extremely grateful
for all the support the members of Clojurists Together have had for CIDER and
friends over the years.

I write this article in the hope that seeing how donations get translated into
real impact (e.g. productivity gains) more companies and individuals will be
willing to help out. CIDER is all of you, the amazing Clojure community. Clojurists together strong!

[^1]: I also hoped the collective would fund more Clojure conference trips for me and other key contributors, but sadly Covid-19 put those plans on halt for quite a while. I haven't been to a Clojure event since Feb 2020...
[^2]: You can find more details in the [changelog](https://github.com/clojure-emacs/cider/blob/master/CHANGELOG.md#180-2023-10-13).
