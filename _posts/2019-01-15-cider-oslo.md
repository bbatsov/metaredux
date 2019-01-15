---
layout: post
title: CIDER 0.20 (Oslo)
date: 2019-01-15 09:51 +0200
category: posts
tags:
- Clojure
- Emacs
- CIDER
---

Hot on the heals of [yesterday's article]({% post_url
2019-01-14-ciders-orchard-the-future %})) I've got a new one for you!
This one is going to be short and sweet - just a few notes about the
release of CIDER 0.20 (Oslo).

I guess some of you are surprised by the news - after all wasn't CIDER
0.19 (Raleigh) released only a couple of weeks ago? Usually CIDER
releases are (many) months apart. Well, while that's normally the
case, I've always wanted to do more focused and frequent releases and
it seems that I managed to succeed this time around.

CIDER 0.20 includes very few changes overall.[^1] I'll cover them briefly here.

<!--more-->

## Cleaning up Shop

CIDER (or `cider-nrepl` to be precise) dropped support for nREPL 0.2 (a.k.a. `tools.nrepl`).
Now you'll need nREPL 0.4+ (ideally 0.5.3+), but that's not a big deal as at this point
both Boot and Leiningen ship the modern nREPL. Just make sure you're on Boot 2.8.2+ or
Leiningen 2.8.3+.

I'll be gradually removing support for nREPL 0.2 from all nREPL
extensions I'm maintaining in the months to come. I'd advise other people
maintaining nREPL extensions to do the same.

## Native nREPL Pretty-printing

CIDER used to rely on a custom middleware for result pretty-printing for many
years now, but nREPL recently added [native support for
pretty-printing
values](https://nrepl.org/nrepl/design/middleware.html#_pretty_printing),
and this rendered our custom approach redundant. The biggest change in CIDER 0.20 is that
it switches to nREPL's built-in pretty-printing API.

Some other related changes:

* Support for `zprint` has been added.
* We're passing to the print engines (e.g. `zprint`) their native
  options, instead of wrapping their invocations in dynamic bindings
  (e.g. `*print-length*`, `*print-level*`, etc).
* Pretty-printing is enabled in REPL buffers by default.
* You can pretty-print results in scratch buffers using `C-u C-j`.

There's now some work underway in nREPL to support [streamed printing
of values](https://github.com/nrepl/nrepl/pull/118). That's going to
be a really great improvement of the current situation as it would
mean faster feedback for the clients and no possibility for
"invisible" printing of infinite/huge sequences on the server-side.[^2]

CIDER now has a new section of its manual [dedicated to pretty-printing](https://docs.cider.mx/en/latest/pretty_printing/).
You should definitely check it out!

Note that the new pretty-printing requires for you to be on nREPL
0.5+. If you're not, you're simply going to get results that are not
pretty-printed.

## Misc

I've spent a bit of time reorganizing [CIDER's
manual](https://docs.cider.mx) and breaking down some ridiculously big
sections (e.g. `Using the REPL`, `Configuration`, etc) into smaller
chunks.  I think you'll find the new layout easier to navigate, but
there's still a lot of room for improvement.

A handful of small changes and bug improvements also made it in this
release, but there's nothing worth highlighting there. Just the usual
quality of life improvements you're bound to expect from each new
release.

On a related note - there's also a [new clojure-mode
release](https://github.com/clojure-emacs/clojure-mode/releases/tag/5.10.0),
that you should certainly check out!

## CIDER Next

I don't have a specific plan for the next release - neither in terms of features nor in terms of a timeline.

I'll be taking some time off from working on open-source now that the new CIDER is out, and I hope that in the mean time
the work on the next nREPL release will be done and I'll just have to incorporate all that awesomeness into CIDER when I'm back.

As some of you probably know, [Christophe Grand](https://github.com/cgrand) has joined nREPL's team and is working on bringing some amazing
features from [unrepl](https://github.com/Unrepl/unrepl) to nREPL:

* The ability to [upgrade a plain socket REPL to nREPL](https://github.com/nrepl/nrepl/issues/96).
* [Sideloading](https://github.com/nrepl/nrepl/pull/109) - allowing clients to act as network classloaders.
* [A rich value printer](https://github.com/nrepl/nrepl/pull/106) with collection elision.

(Extremely) Exciting times ahead! I cannot thank Christophe enough for
the amazing work he has been doing on nREPL recently!  People like him
are the heart and soul of the Clojure community and the reason why I
love being part of it!

## Epilogue

The year had an explosive start for me - so many project release, so
much open-source work done!  I was really eager to start "clean" and
finish many of the initiatives that have been dragging throughout the
past year. It was pretty painful and exhausting, but I got to a point where I did
everything I felt I needed to do (for the time being) and now I'm
looking forward to some respite and re-energizing myself.

While, I'm gone the development won't stop, of course. In my absence
the other CIDER and nREPL Core Team members will be reviewing PRs and
cutting releases. I hope you'll surprise me with many great
developments next time I open my GitHub!

Enjoy CIDER Oslo (ir)responsibly! Keep hacking!

[^1]: https://github.com/clojure-emacs/cider/releases/tag/v0.20.0
[^2]: Those are hard to spot and result in out of memory errors in nREPL.
