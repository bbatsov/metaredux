---
layout: post
title: "CIDER's Orchard: The Future"
date: 2019-01-14 10:58 +0200
category: posts
tags:
- Clojure
- Emacs
- CIDER
- Orchard
- nREPL
---

## Prologue

This post is part of the mini-series "CIDER's Orchard". That's
intended to be the final article in the series, but this might change
depending on my inspiration.

* [CIDER's Orchard: The Heart]({% post_url 2018-11-09-ciders-orchard-the-heart %})
* [CIDER's Orchard: The Periphery]({% post_url 2018-11-11-ciders-orchard-the-periphery %})
* CIDER's Orchard: The Future

<!--more-->

## The Road so Far

Let's start up with a brief summary of the problems outlined in the previous two articles:

* There are many projects in CIDER's Orchard.[^1]
* There are very few people working on them.
* Half the projects in the Orchard are barely maintained.
* The few gardeners burn out rather quickly due to the huge amount of work.
* There's almost no funding.
* We need to push harder to get feature parity for Clojure and ClojureScript.

As a result, progress is slow and erratic. The quality of the libraries
is suboptimal[^2], as to make any progress with our limited resources
we're forced to cut corners.

So, what can we do?

## Structure

The inception of [CLJ Commons](https://github.com/clj-commons) got me
started on whether there should be a more formal structure around the
projects in Clojure's Orchard.  Right now some projects live in the
[Clojure Emacs GitHub organization](https://github.com/clojure-emacs),
which might be sending the wrong message out there - namely that they
are Emacs specific. Not a big deal, at this point, by maybe something
worth revisiting down the road.

I've also been wondering how to make sure that all tool writers, who
are leveraging projects from the Orchard have a sit at the table. I
definitely don't want to create a perception that CIDER's development
drives the development of all related libraries. That should ideally
be some collaborative process. I attribute the current state of affair
mostly to historical reasons - CIDER simply kicked off many of the
projects.

Anyways, to summarize this point - down the road we might establish
some `clj-orchard` (or whatever) organization to house libraries like
`cider-nrepl`, `refactor-nrepl`, `orchard`, `cljs-tooling`, `sayid`,
etc, but that's not a high priority item on the agenda today.

## The Orchard is Hiring

I've been involved with some of the Orchard's projects for over 6
years now and I'm the only person who has stuck around since the very
beginning of CIDER (named `nrepl.el` back in the day). That's pretty
depressing on its own, but as the scope of the Orchard kept growing,
and with it the pressure on me, I find myself extremely tired and
stressed most of the time.

Even though Clojure is still my favourite programming language and the
CIDER-related projects are my favourite side-projects, I don't enjoy
working on them as much as I once did. Sometimes it almost feels
like a second job that pays nothing and just stresses me a lot. I
won't go into any details about the hardships of maintaining
open-source projects, though.  That's not the point of this article.

I've said recently that regardless of my frustrations I don't
subscribe to Rich's perspective that [Open-Source is not About
You](https://gist.github.com/richhickey/1563cddea1002958f96e7ba9519972d9). Obviously,
that's something extremely subjective, but my belief is exactly the
opposite - open-source is all about you! That's what got me into open-source
to being with - the chance to make a difference, the opportunity to
influence the tools that I'm using on a daily basis, the chance to do
something useful for others.

So, if you want to make a difference - then just go and make a
difference!  Join any of the Orchard's projects and help make them
better! Our community is extremely welcoming and supportive, and
there's no improvement that's too small not to be considered valuable.
Many people wonder if they'd be skilled enough to contribute - after
all tooling projects are notorious for being very complex, hard to
understand and requiring in depth knowledge of the language and its
respective platform. While some mastery of Clojure, ClojureScript, the
JVM and JavaScript would certainly help, I definitely don't think that
there's any rocket science involved in most parts of the Orchard. Just
think on this for a moment - I've worked on all those projects for
years with almost no commercial exposure to Clojure.[^3]

Side note - I'm constantly depressed by the passiveness of the Clojure
community when it comes to open-source projects of any kind - even the
most established projects (tools and libraries) don't really get a lot
of contributions, and that's such a sharp contrast from the Ruby
community, where I spent most of my time.

Perhaps Clojure itself set the tone for this, perhaps it was Java,
perhaps our community is just small, I don't know.[^4] But I do know
that we can change this! If someone has any ideas about how we ended
up where we are - please, share this with me in the comments.

Hacking on the libraries themselves is certainly not the only way to
contribute. In the next few section I'll go over a few other options
you might want to consider.

## Documentation

One of the easiest way to start contributing is by working on the
documentation. I've spent quite a lot of time working on
[cider.mx](https://docs.cider.mx) and [nrepl.org](https://nrepl.org),
but still the manuals are pretty bad by my personal
standards. Producing high quality documentation take a lot of work and
I simply don't have time to do it.

I'd really appreciate more help there. Also, working on the docs is a
great way for you to deep dive into a project and understand it
better.

For nREPL I've been experimenting for a few months with
[AsciiDoc](https://asciidoctor.org/) and [Antora](https://antora.org/)
and I've been pretty pleased with the results.  One great feature of
Antora is that you can keep documentation for related projects in the
repository of each project and weave the separate documentation
modules together in a single site at the end. At some point the plan
is to have Antora documentation modules in Piggieback's repo, in Drawbridge's
repo and so on, and that's a great opportunity for you to help out.

I really hope that one days CIDER will also get a nice Antora-powered
site, which documentation modules pulled from projects related to
CIDER as well (e.g. `cider-nrepl`, `clj-refactor`, etc). This will
require a lot of work, and it's another opportunity for people to contribute.

## Helping with the Backlog

There are so many open issue across all the projects that I don't even have time
to go over them, respond to them, test whether bug reports are really bugs, or
some misconfiguration, and so on.

I would certainly appreciate some help in this department!

We also need to reviewl all long-standing tickets, prioritize the most important ones
and kill the rest.[^5]

Reviewing PRs is also a great way to help, although we don't really get that much of them,
so they are not really a big bottleneck right now.

## CI

On the CI front I'd really love for all the projects in the Orchard
that I can directly control to switch from Travis CI to Circle CI, as
the instability of Travis's service is wasting a lot of time for
everyone involved with projects in the Orchard.

That's not a lot of work, but someone has to do
it. [Arne](https://github.com/plexus) is already tackling this for
`cider-nrepl`, but that's one of many projects.

It would also be great if someone developed an automated way to
rebuild the Antora docs - basically that'd probably be one repo
notifying another repo on push that the documentation should
potentially have to be rebuilt and redeployed.

## Funding

We're all busy people I get that. Time is the most valuable asset everyone has -
I also get that. This means that's reasonable to expect that everyone can
allocate time to help out by doing some work on the Orchard's projects.
But I'm reasonably certain that most of you (and your employers) can help
financially. Let's examine the current situation.

[CIDER's OpenCollective annual
budget](https://opencollective.com/cider) is around $4000 and about
half of it was contributed by a handful of people. You have to agree
that's not enough money even to cover travel expenses for a few Clojure
conferences, let alone leave you some money you can contribute to your
family's budget.

I really think the work I've done (not to mention the great work
others have done) is worth a bit more than this, and I hope that
people are going to realize this before I decide to throw in the towel
and call it quits. With the current level of funding we'd never have
dedicated people working on better Clojure tooling, and we'd never to
be able to pay people to work on important features in some focused
manner (think of this of self-contained Clojurists Together).

As dual-licensing for CIDER won't help much (I expect it would
generate a lot of negativity and CIDER's just one piece of much bigger
puzzle), I'd like to give the donations one last chance. Here's
something simple I'd like you to think about. If a Clojure IDE is
worth about $100/year, I think that the work on CIDER and all the
Orchard's libraries might be worth something as well.

* If you're a company that does a lot of Clojure development, perhaps
  you can shell out something like $100-$500/month for CIDER's
  OpenCollective. For any software business that's a drop in the pond and
  probably your team getting better tools will more than make up for
  your "investment" in the future.
* If you're an individual working in a Clojure company and you're
  relying on tools like CIDER, nREPL, piggieback, etc - try to get
  your employer to contribute something like $5/person/month to the
  OpenCollective.

Even if your company is not supportive, but you're a Clojure
professional, perhaps you should consider supporting the tools that
help you get your done. Or have some fun.

Ideally, more people should support [Clojurists
Together](https://www.clojuriststogether.org/) as well, but that's a
different topic altogether.

I don't want to discuss at all the topic of one-off crowdfunding campaigns.
I'm looking for long-term sustainable solutions, instead of short-term ones.
While I'm fairly confident that a fund-raiser might work out well once or twice,
I don't really want to deal with everything that they entail and I don't have
to repeat the same exercise over and over again.

Let me remind you that the name _CIDER's Orchard_ is really a misnomer
at this point - it's **Clojure's Orchard** and it has been Clojure's
Orchard for a while now.  I've yet to see donations work out well for
any open-source project, but you've got a great opportunity to prove
me wrong!

## Epilogue

That's a wrap! It took me a while to get to writing this post, and
frankly I don't even recall what I intended to write originally, but
I'm glad I'm done with it now.

I'm happy to see that my recent posts ignited the interested in nREPL,
but I'd love to see us making progress all across the Orchard!  If we
fail to do so - sooner or later we'll have to deal with the collapse
of the current model, which would be an unpleasant scenario for sure.
That's why I keep writing more posts that ultimately aim to get
more people involved with Clojure's tooling, so we can build a tooling
that matches the greatness of the underlying language(s).
I probably sound like a broken record by now, but that's an important topic, and
I want people to think about it.

Thanks for reading that article! Hopefully it was somewhat useful and very inspirational!

And a big thanks to everyone who had supported in one way or another
**Clojure's Orchard**, so we can get where we are today!  Hack long
and prosper! In parentheses we trust!

[^1]: And the previous projects didn't even cover the many Emacs Lisp projects that are related to CIDER.
[^2]: At least by my subjective standards.
[^3]: As a disclaimer - I did work for a long time as a Java developer back in the day.
[^4]: Some people want to believe that Clojure software is just better and probably doesn't need as much work, as software written in other languages, but knowing the things I know I find this opinion extremely far fetched.
[^5]: Unless this article generates so much excitement that we suddenly have a big enough team to tackle all the open tickets.
