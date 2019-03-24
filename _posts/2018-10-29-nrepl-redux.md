---
layout: post
title: "nREPL Redux"
date: 2018-10-29 08:27:58 +0200
categories: posts
tags:
- Clojure
- nREPL
- Meta
- Emacs
- CIDER
---

## Prologue

Naming is hard! It truly is! I've spent a lot of time ruminating on
the title for that particular post, before coming up with "nREPL
Redux".  I could have just as easily gone with something like "nREPL
Reborn", "nREPL Returns", "nREPL Forever", "nREPL Strikes Back" or
"The Revenge of nREPL".[^1] Anyways, the name doesn't matter that
much - what matters is the story I'm about to tell
you.  Namely the story of the rise, fall and re-birth of the most
important tool in the Clojure ecosystem today.

If you're into Clojure development you probably know that I'm the
author of [CIDER][] - a (somewhat) popular Clojure programming
environment.  Most people think of CIDER as an Emacs project, but it's
much bigger than this - there are so many moving pieces that make the
whole thing work, that I should probably write separately on this
subject (and I totally plan to do so). One piece is special,
though. It's the very heart of CIDER and it's the [nREPL][] server
which powers all the magic that happens behind the scenes - code
evaluation, code completion, debugging and all that jazz.

nREPL is, very simply put, a network REPL server, which aims to provide
a common ground for Clojure tool authors who need access to a REPL.
This post is mostly about nREPL, but it's also about some bigger topics
like building inclusive communities, avoiding past mistakes and driving
progress forward. So, here we go...

<!--more-->

## A Long Time Ago in a REPL Far Away....

In the early days of Clojure people would often praise the ingenious
design of the language and the brilliance of Rich Hickey, but would
shy away from the topic of development tools for Clojure, as there
weren't that many to go around.

A [SLIME][] version tweaked for Clojure was the only real game in town
early on, and Emacs quickly became the most popular editor for Clojure
hackers. Later on, Eclipse and NetBeans plugins for Clojure emerged as
well, and everyone rolled their own implementation of a REPL server that
powered the interactions users had with Clojure.

I [spoke about this at length](https://www.youtube.com/watch?v=4X-1fJm25Ww&) at Clojure/conj
2014 and there are some really awesome nREPL design documents, like
[this one](https://docs.google.com/document/edit?id=1dnb1ONTpK9ttO5W4thxiXkU5Ki89gK62anRqKEK4YZI&authkey=CMuszuMI&hl=en#),
that tell even more of the story.

nREPL was created to tackle the problem of the missing common
infrastructure for Clojure development tools and it took the Clojure
world by a storm. In the span of a couple of years (2010-2012) pretty
much all the Clojure development tooling re-aligned itself around
nREPL and the project achieved its ambitious original objective. I
guess almost all of you have seen this:

```
$ lein repl

nREPL server started on port 54588 on host 127.0.0.1 - nrepl://127.0.0.1:54588
REPL-y 0.3.7, nREPL 0.2.12
Clojure 1.8.0
Java HotSpot(TM) 64-Bit Server VM 10.0.1+10
    Docs: (doc function-name-here)
          (find-doc "part-of-name-here")
  Source: (source function-name-here)
 Javadoc: (javadoc java-object-or-class-here)
    Exit: Control+D or (exit) or (quit)
 Results: Stored in vars *1, *2, *3, an exception in *e

user=>
```

Most build tools today start nREPL servers and every editor/IDE out
there provides some form of nREPL integration. CIDER itself was built
with the explicit objective to replace SLIME with an nREPL-powered
tool. Some of you might even remember that the first few versions of CIDER were
actually named `nREPL.el`.[^2]

## Clojure Contrib Strikes Back

nREPL started as a personal project of Chas Emerick @
https://github.com/cemerick/nREPL in 2010. After its initial success,
it was invited to be one of the initial three projects in the "new"
[Clojure
Contrib](https://dev.clojure.org/pages/viewpage.action?pageId=5767464)
family of projects later that year, and thus moved to
https://github.com/clojure/tools.nrepl.

This was supposed to be a step towards nREPL becoming part of Clojure
at some point, but alas - things took a pretty different turn. The
project was doing great while Chas was committed to it, but his life
and work priorities shifted and the development slowed
down significantly. I guess you might be wondering why would something
like this happen? Didn't I just say that nREPL was a crucial piece of
the Clojure ecosystem? After all it's open-source software and there
should be people willing to contribute, right?

In theory - yes, in practice - not so much. I might be completely off
when I say this, but I attribute the lack of contributors mostly to
the heavy-handed contribution process for Clojure Contrib
projects. For those of you who are not familiar with Contrib, here are
the highlights of this process:

* Code is hosted on GitHub.
* You need sign a contributor agreement (CA) to contribute.
* You have to use JIRA for issues and patches. Collaboration via GitHub Tickets/PRs is a no go.
* You have to use Hudson (!!!) for CI.
* You have to use Maven (all contrib projects share a common Maven template).

You can imagine that most people (me included) are not exactly
thrilled to jump though all those hoops. The numbers speak for
themselves - most Clojure Contrib projects have between 1 and 0
maintainer. Sometimes the original authors of key projects would lose
interest in the project, but no one would take over. My guess as to
why this is happening is the nature of the contribution process.

I also find it somewhat amusing that no project in Contrib has ever
been promoted to Clojure itself, so this really begs the question of
what exactly is the goal for the entire endeavour, but I'll leave this
subject for another article.

## A New Hope

In the mean time I was trying to build a great Clojure IDE on top of
nREPL and I was getting really frustrated with the nREPL situation, as
many of the features I wanted to build or problems I wanted to address
required changes to nREPL. I was really willing to contribute, but I
definitely value my limited time way too much to be wasting it on a
process with which I completely disagree.

The people who know me are aware of my immense passion for [free
software projects](/projects) and that I always want to build
healthy communities around my projects, nurture contributors,
etc. Clojure Contrib goes against everything I hold near and dear! I
could have probably gotten commit access to the nREPL project in
Clojure Contrib, but this wouldn't have been a solution to the real
problem (an environment that discourages contributions), so I opted
not to go in this direction. Instead I decided to go all in on this
and to propose something novel and somewhat unorthodox...

I started advocating strongly for migrating nREPL out of Clojure
Contrib[^3] and rebooting the project's development in a truly free and
welcoming manner. Eventually, Chas agreed that's the reasonable
course of action and announced his intention to migrate nREPL out of
Clojure Contrib in the summer of 2017
(https://github.com/nrepl/nREPL/issues/1), with the objective of
reconstituting an environment where the people most interested in and
impacted by nREPL could participate in its development as easily as
possible.

You have no idea how excited I was about all of this! However, things
are never as easy as they sound. Little did I know back then that I'll
be the one who'll actually have to carry out the migration.

## Rogue One

Chas did some of the groundwork for migrating nREPL out (he
re-licensed the project, re-created the Git repo, expunging all the
irrelevant commits, etc), but then he ran out of time for this and I
offered my assistance with the outstanding tasks. I wanted to show
that I'm not just one of those people who complain and don't mean what
they say. nREPL was truly free at this point and this meant that I had
no reservations to jump in and help out. Still, I wish I understood
better what I was signing up for back then - it was way more work then
I could possibly imagine...

The first steps were pretty easy and clear:

* We needed to "rebrand" the project and change its deployment artifact and namespaces, as technically
this was a new project (even though in spirit it was a continuation of the old one).
* We needed to update all popular extensions (middleware and transports mostly) to support the new nREPL.
* We needed to fix some bugs and implement some long overdue features.
* We needed to clean up some legacy code.

At the time, however, I didn't realize that there would also be a lot
of effort to update nREPL in various downstream tools
- in particular Leiningen and Boot. Unfortunately releases there don't
happen very often and while the latest stable Boot switched to the new
nREPL already, Leiningen still has to cut a release targeting it. This
meant I had to do a lot of extra work to support both `tools.nrepl`
and the new nREPL in many projects.

I also didn't anticipate that a lot of the key nREPL middleware were
also somewhat unmaintained and I had to personally go and update all
of them ([Piggieback][], refactor-nrepl, sayid, etc). Same with the
popular [Drawbridge][] HTTP transport.  Eventually this lead to the
creation of a [new nREPL GitHub org](https://github.com/nrepl), that
houses the "core" nREPL projects and to me getting really familiar
with nREPL and its ecosystem. So, familiar that at some point I even
decided to create a [proper manual for nREPL][nREPL], which is one of
the most time-consuming tasks I've been working on lately.

All of this resulted in an extremely busy and frustrating summer for
me and a lot of work I didn't see coming, but I'm really glad I
managed to pull through and now we're finally starting to reap the
benefits of it all. Right now every meaningful piece of the Clojure
development ecosystem is compatible with the new nREPL and I hope
that in a year or so we'll get rid of all the compatibility code
and clean up shop a bit.

I want to extend a special "Thanks!" to my
friends in the London Clojurian community and Clojurists Together who
kept me going, even when I had lost hope that what I set out to do was
doable. I couldn't have made it without you!

Now I feel as burned out as shit, but I'm also extremely happy that I
got us where I wanted us to be!

## Interlude - The Socket REPL

I believe some of you might be wondering if all this hard work was
really worth it. After all Clojure now has the [Socket
REPL](https://dev.clojure.org/display/design/Socket+Server+REPL) and
if that's part of Clojure probably we're supposed to use it, right?

There are also some interesting projects like
[unrepl](https://github.com/unrepl/unrepl) and
[prepl](https://www.reddit.com/r/Clojure/comments/7wdlq4/first_cut_of_prepl_%E8%B7%AF_clojureclojure86a158d/)
that build on top of it to provide a similar functionality to the one
offered by nREPL.

I saw a couple of days ago an [amazing tweet by Joe
Armstrong](https://twitter.com/joeerl/status/1055815315685367815) (of
Erlang fame), that perfectly reflects how I feel about all of this:

> The groundhog day development method
>
> 1) Invent a new simple thing
>
> 2) Add features
>
> 3) Add more features
>
> 4) Add more and more and more and more features
>
> ...
>
> N-2) It's so complicated nobody can use it
>
> N-1) Throw everything away
>
> N) goto 1)
>
> Is there a way out?
>
> Maybe :-)
>
> -- Joe Armstrong

Technical merits and personal preferences aside, people should
understand that the Clojure community has made an epic investment in
building tools of top of nREPL and that going back to 1) will set us
back a lot. I know I'm never putting in the work to add support for
the Socket REPL to CIDER myself. I believe that nREPL is exactly what
I need and with a little bit of work to enhance it, we can get a lot of
more of value for a fraction of the investment. And we can do this
_today_.

I've put in a lot of work to make sure the underpinnings of CIDER like
[orchard](https://github.com/clojure-emacs/orchard),
[cljs-tooling](https://github.com/clojure-emacs/cljs-tooling), etc are
not nREPL specific and other tool authors can leverage those, so no one has to
re-invent the wheel again, but that being said, I really believe that
we already have the flexible and solid foundation that we need and we
should just work towards making it better.

There's also the fact that unlike the Socket REPL and prepl, nREPL is
free from the shackles of the Clojure (Contrib) contribution
process. Don't get me wrong - the fact that I'm (often) critical of
Contrib doesn't mean I'm disrespectful to Rich's and Cognitect's
decisions and I think they are doing something inherently wrong. I
just think that what works for Clojure and projects that might
(really) end up being part of Clojure (e.g. `tools.reader`,
`core.async`), doesn't really make sense for something like a REPL
server. I think that we really should try to have as little
functionality as possible tied directly to Clojure, so we can be more
nimble and flexible when it comes to fixing and improving
things. Probably if the Socket REPL was at least a Contrib project I'd
have more faith in it, but I definitely don't want to wait one year
for changes that can be done today, and perhaps some of you share my
perspective on this.

nREPL is that solution. Free of Clojure's process, free of Clojure's release
cycle, open for business.  This post has several goals and the most
important one of them is to invite all of you to help get nREPL to the
next level.

On top of this - nREPL is also mature, stable, battle tested and with
an incredibly flexible design.  I think that 90% of the people who
complain about nREPL's limitations simply don't understand how easy it
is to do with it anything you can possible wish for. The
[transports](https://nrepl.org/nrepl/design/transports.html)
abstraction, in particular, is mind-blowing.

## The Small Victories

In the case you're wondering how much the new project has achieved so
far, I'll let the [changelog](https://github.com/nrepl/nrepl/blob/master/CHANGELOG.md)
speak for that. In a nutshell:

* We've fixed plenty of annoying bugs.
* We now have awesome support for using nREPL together with `clj` and `tools.deps`.
* We have awesome nREPL documentation.
* We've added support for hot-loading dependencies in nREPL.[^4]
* We're close to adding authorization support to nREPL. ([PR](https://github.com/nrepl/nrepl/pull/46))
* We're close to shipping host-wide and project-specific config support for nREPL. ([PR](https://github.com/nrepl/nrepl/pull/66))
* We're about to ship built-in support for pretty-printing.

And we did all of this without breaking any backwards compatibility
(apart from the unfortunately namespace changes that were
unavoidable). We're committed to continue to develop the project
is a stable and responsible fashion.

I've got a small hint for you - nREPL 0.5 is going to be big!

And yeah - I used "we" instead of "I" intentionally. But more about
this in a moment...

## The Big Victories (The Return of the Jedi)

The biggest validation of all the work that went into re-booting nREPL is the amazing
involvement of the community. Most of the nicest improvements were not contributed
by me and that re-affirms my belief that if you create the right environment, people
would rally around and drive progress and innovation forward.

I hope that down the road a new steward for the project will emerge
and I'll be able to take a step back into the shadows and switch back my
focus to hacking on CIDER. I don't see myself as the long-term maintainer
of nREPL - I see myself simply as an agent of change, whose main objective
is to set the project up for success.

We're just getting started and I'm sure the best is yet to come!

## Help Wanted

All of the projects in the nREPL org could benefit from more
maintainers. It's especially important to find stewards for key
projects like [Piggieback][] (ClojureScript support for nREPL) and
[Drawbridge][] (HTTP support for nREPL).

We're also looking for someone willing to port [nrepl.bencode](https://github.com/nrepl/bencode) to
ClojureScript, so it's easier to write ClojureScript clients for
nREPL. That's pretty much the only reason why we've extracted the bencode functionality
out of nREPLs core.

nREPL's manual needs proofreading, editing and more content
(especially when it comes to documenting how to extend nREPL with new middleware
and transports).

The project [still needs a
logo](https://github.com/nrepl/nrepl/issues/30) and we really want to
add a couple of extra transports -
e.g. [EDN](https://github.com/nrepl/nrepl/issues/60) and
[Transit](https://github.com/nrepl/nrepl/issues/61).

There's also the big dream to port nREPL to ClojureScript as well, so it can be used
in self-hosted environments. How awesome would that be, right?

There's plenty of work around and no contribution is too small! Join us in nREPLness!

## Epilogue

Telling stories like that one is the main reason I decided to revive my writing
efforts. I think they truly are worth sharing.

That's from me, folks! Keep hacking!

[^1]: Probably by now you've realized I'm a huge Batman and Star Wars fan.
[^2]: Naming is hard!
[^3]: To my knowledge this had never happened in the past.
[^4]: That's big! Take a look [here](https://nrepl.org/nrepl/usage/misc.html#_hot_loading_dependencies) for details.

[CIDER]: https://www.cider.mx
[nREPL]: https://nrepl.org
[SLIME]: https://common-lisp.net/project/slime/
[Piggieback]: https://github.com/nrepl/piggieback
[Drawbridge]: https://github.com/nrepl/drawbridge
