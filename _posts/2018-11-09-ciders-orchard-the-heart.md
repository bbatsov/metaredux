---
layout: post
title: "CIDER's Orchard: The Heart"
category: posts
tags:
- Clojure
- Emacs
- CIDER
- Orchard
- nREPL
---

## Prologue

This post is part of the mini-series "CIDER's Orchard". Currently
I'm planning 3 installments in the series, but this might change
depending on my inspiration.

* CIDER's Orchard: The Heart
* [CIDER's Orchard: The Periphery]({% post_url 2018-11-11-ciders-orchard-the-periphery %})
* [CIDER's Orchard: The Future]({% post_url 2019-01-14-ciders-orchard-the-future %})

<!--more-->

## It Starts with an Orchard

> You need a great orchard in order to brew the finest cider.
>
> -- Anonymous

Throughout my career I've had the following observation - all
software engineers want great development tools. That makes perfect sense -
better tools mean better productivity, more enjoyable work and more
time to focus on solving real problems.

I've also had another related observation - almost all software
engineers expect that those great development tools are going to build
themselves.[^1] It's pretty much the same with software companies -
most of them just take development tools as a given (especially if
they are open-source) and are unwilling to invest in them. Clojure's
ecosystem is no exception.  This has always puzzled me, because if I
were running a company I'd certainly want to put the best possible
tools in the hands of my employees, so I can maximize their output and
impact.

[^1]: <https://en.wikipedia.org/wiki/Tragedy_of_the_commons>

Many people think of CIDER as a single (Emacs) project, but that's not
really the case. In fact the reality is quite different from that
common perception. The vast majority of CIDER's functionality lives
in many Clojure libraries, that are general-purpose and have no
specific ties to Emacs and CIDER.  I call all those libraries and
tools "CIDER's Orchard"[^2] - the fertile ground on which Clojure
tooling grows and thrives.  My use of the word _Clojure_ here is
deliberate, as these days a lot of Clojure development tools share
that same foundation.

[^2]: Not to be confused with the [orchard][] library.

Before we continue here's a quick refresher on CIDER's basic architecture:

![CIDER Architecture](/assets/images/cider_architecture.png)

Clojure(Script) code gets executed by an nREPL server. CIDER sends
requests to the server and processes its responses. The server's
functionality is augmented by additional nREPL middleware, designed
specifically to address the needs of an interactive development
environment like CIDER (e.g. code completion, code lookup,
documentation lookup, code reloading, macroexpansion, etc).

Technically, the use of nREPL middleware is optional, as we could
have just as easily dumped a bunch of libraries on the user's
classpath and evaluated code from them directly. As a matter of fact -
that's exactly what early versions of CIDER were doing.

The middleware approach has a few distinct advantages:

* You can easily massage data in the middleware and return it to
  clients in an easy to consume format. Early versions of CIDER had
  some really gnarly Clojure code embedded as huge strings in the
  Emacs Lisp code. This was needed because a non-Clojure client can't
  easily parse arbitrary EDN data and we always had to convert the
  data returned by Clojure libraries to a simpler format that's more
  suitable for the use-cases of an interactive programming
  environment.
* The middleware provides valuable abstractions like sessions, that
allow us to cleanly separate user evaluations from tooling evaluations
(e.g. you don't want the code that CIDER ran to get some completion
candidates to obscure your `*1`).
* Middleware can transform a request/response in a transparent
  fashion - this allows you decouple very well different
  responsibilities in your code (e.g. pretty-printing can just be some
  middleware that modifies the values returned by an eval middleware,
  and some authorization middleware can check for credentials and dangerous code
  prior to sending off the code for evaluation).
* It's easy for clients to determine the capabilities of a REPL
server. You can just ask the server about the operations it supports
and decide what to do on the client side based on the server's
response.

## The Heart of the Orchard

CIDER's Orchard is pretty big these days and a single post can't do it
justice.  Today I'll cover only the more important areas of CIDER's
Orchard. All of them are in essence Clojure libraries, and they
underpin many of the core features of CIDER.

I'll give only a very
brief overview of most projects in the Orchard and I might get to
write in more details about some of them down the road.

### nREPL

At the center of it all is [nREPL][].

I recently [wrote extensively about its importance]({{ site.baseurl }}{% post_url 2018-10-29-nrepl-redux %})
, so I'll keep this section light.

### cider-nrepl

[cider-nrepl][] is basically a thin wrapper around many Clojure
libraries, that provides an nREPL interface for working with
them.  There's almost no "business" logic in `cider-nrepl`, except for the
debugger, the test runner and the stacktrace processor.

`cider-nrepl` started out as a CIDER-specific thing, but it became
something much more than this.  Very little functionality in
`cider-nrepl` is CIDER-specific and today several other Clojure
programming environments are using it (e.g. `vim-fireplace`,
`iced-vim`, `calva`). In hindsight I deeply regret naming it
`cider-nrepl`, as `orchard-nrepl` would have been a more fitting name
and would have made it more apparent that `cider-nrepl` is very useful
outside the context of CIDER.

#### Project Status

`cider-nrepl` has a couple of maintainers - me and Vitalie Spinu.
We're also fortunate to be getting some outside contributions from
time to time. It was really great to
get a couple of PRs from the author of `vim-iced` recently!

On the negative side - the project has lots of [open
issues](https://github.com/clojure-emacs/cider-nrepl/issues) and a lot of
gaps when it comes to ClojureScript support.

There's also the fact that my attention is spread over many projects,
and lately I've had almost no time for `cider-nrepl`.

### Piggieback

[Piggieback][] adds ClojureScript support to nREPL. Improving
Piggieback is key to having a better ClojureScript experience in
CIDER (and any other nREPL client).

#### Project Status

Piggieback currently has just one maintainer and a half ([Bruce
Hauman](https://github.com/bhauman) and me).  I count myself as half a
maintainer as my knowledge of ClojureScript is pretty basic and I
certainly can't tackle anything important or complex there.

There are plenty of cool ideas in the backlog ([this one being my
favourite one](https://github.com/nrepl/piggieback/issues/73)), but we
certainly need some help to make those happen!

### Orchard

Originally [SLIME][] was the most popular way to program in Clojure
with Emacs and a lot of useful functionality was created for it to
support things like code completion, value inspection, finding
references, apropos and so on. This functionality was implemented as a
`swank` adapter written in Clojure and lived in the [swank-clojure][]
project.

Subsequently [CIDER][] and [cider-nrepl][] replaced SLIME and `swank`,
and much code was moved from `swank-clojure` to `cider-nrepl` and
continued to evolve there.

You can watch the presentation [The Evolution of the Emacs tooling for
  Clojure](https://www.youtube.com/watch?v=4X-1fJm25Ww&list=PLZdCLR02grLoc322bYirANEso3mmzvCiI&index=6)
  to learn more about all of this.

The `orchard` project is an effort to prevent repeating the mistakes of the
past - `cider-nrepl` was split into two libraries, so that non-nREPL
clients can make of use of the general functionality contained in
`cider-nrepl` (e.g. things like `apropos`, `inspect`, etc).

Much of the tooling code required to build Clojure editors and smart REPLs
is tool-agnostic and *should* be reused between tools, instead of copied
and altered in each and every tool.

As of the time of this writing most of CIDER's core functionality
lives in `orchard`.  Once the library is feature-complete using a
middleware-less approach is going to be become much easier for those
who prefer it (e.g. you can use `orchard` easily in combination with a
plain socket REPL or nREPL without `cider-nrepl`).

#### Project Status

Currently I'm `orchard`'s only maintainer, but I hope that's going to
change at some point. I believe this library has great potential and
that we, as a community, should invest more effort into it.

My most important objective for it is to eliminate all deps it has and
make it completely self-sustained (as it uses just a bit of
`java.classpath` and `tools.namespace` that's not very hard to do).  A
secondary objective is to complete the extraction of functionality
from `cider-nrepl` - a simple, but time-consuming process.

### cljs-tooling

[cljs-tooling][] is basically the ClojureScript counter-party to
`orchard`. It powers the core ClojureScript functionality like code
completions, var info, etc.

In an ideal world the library would have been named `orchard-cljs`,
but you know how it is - naming is hard.

`cljs-tooling` predates `orchard`, therefore the inconsistent naming
and artifact ids. Down the road we can still see
`[cider/orchard-cljs]`, though. That's not really important at this point -
our focus should be on getting to feature parity with the functionality
provided by `orchard` and `compliment` and that's going to require quite
a lot of work.

#### Project Status

`cljs-tooling` doesn't have any maintainers right now. The project was
actually on ice for almost 3 years, before
[Andrea](https://github.com/arichiardi) started hacking on it a few
months ago. We could really use the help of some ClojureScript masters
here!

### compliment

[compliment][] powers the Clojure code completion. It's the only
library in the Heart of the Orchard, that didn't originate within the
scope of the development of CIDER and `clj-refactor`.

#### Project Status

`compliment` is an amazing library with an [amazing
maintainer](https://github.com/alexander-yakushev). As far as CIDER goes
there's nothing important that needs doing there.

The only big item remaining is to replace `clojure-complete` with
`compliment` in the broader Clojure ecosystem. A task that I've
[started on](https://github.com/trptcolin/reply/pull/153) and
miserably failed. Hopefully one of you, dear readers, is going to
pick up the slack and drive this to completion.

I'd like to extend a huge "Thanks!" to Alex Yakushev for the awesome
work he has done with `compliment` for so many years now! He certainly
deserves more than one compliment! We need someone like him for
every project in The Orchard!

### MrAnderson

One thing that's tricky when writing development tooling is dependency
isolation.  You're normally running the development tools and the
actual application that someone's developing in the same process,
which means there's some chance that your development library and the
user code are going to depend on the same libraries.

Most of CIDER's deps are something that would normally not be a part
of an application (e.g. why would you need `compliment` in an app),
but still you can run into problems, especially in teams where people
are using different programming environments. Let me illustrate this
with an example - CIDER users would never use something like
`tools.namespace` or `tools.tracing` directly, but users of some other
editor might very well want to do so and add explicit deps to those
libraries in the dev profile of their applications.

To prevent such conflicts `cider-nrepl` uses [MrAnderson][] - a tool
for rewriting and inlining dependencies. It works both on Clojure and
Java deps (it changes the namespaces for Clojure deps and the packages
of Java deps).

#### Project Status

`MrAnderson` has been serving us well for years, but it definitely has room for improvement
and I know that its author could use some help getting `MrAnderson` to the next level.

In particular, I'd love it if someone fixed [this
bug](https://github.com/benedekfazekas/mranderson/issues/7).

## Interlude - The Orchardists

One things that really depresses me these days and how small the team
maintaining The Heart of the Orchard is.

Out of a dozen or so awesome Clojurians who were working on
CIDER-related tooling in the early days, only me, Vitalie and Alex are still
very actively involved with the projects.

We really need our new Jeff Valk, Artur Malabarba, Michael Griffiths, Hugo Dundan...
If we're looking for new open-source gigs - we're certainly "hiring".

My "nREPL Redux" post managed to get some people excited about nREPL and the project
received a record number of contributions afterwards.[^4] I can only hope that
we can replicate this effect on a larger scale!

Here and there in the post I've referenced some high-impact tasks that
you might want to tackle, but I'd welcome each and every contribution,
no matter how small it is.

[^4]: Including a great [new logo](https://github.com/nrepl/nrepl/pull/71).

## Epilogue

We've barely scratched the surface of CIDER's Orchard, but it should already be apparent
there's a lot going on and plenty of work to go around.

You probably saw a recurring theme in the post - we really need more ClojureScript hackers
to help with `piggieback`, `cljs-tooling` and `cider-nrepl`. We're still dreaming of
[porting the debugger to ClojureScript](https://github.com/clojure-emacs/cider/issues/1259).

Stay tuned for the next installment where we're going to venture to
the outskirts of CIDER's Orchard and examine the many projects that
dwell there.

[CIDER]: https://github.com/clojure-emacs/cider
[nREPL]: https://nrepl.org
[MrAnderson]: https://github.com/benedekfazekas/mranderson
[Piggieback]: https://github.com/nrepl/piggieback
[SLIME]: https://github.com/slime/slime
[swank-clojure]: https://github.com/technomancy/swank-clojure
[cider-nrepl]: https://github.com/clojure-emacs/cider-nrepl
[compliment]: https://github.com/alexander-yakushev/compliment
[orchard]: https://github.com/clojure-emacs/orchard
[cljs-tooling]: https://github.com/clojure-emacs/cljs-tooling
