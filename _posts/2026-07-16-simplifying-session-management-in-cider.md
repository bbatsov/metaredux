---
layout: post
title: Simplifying Session Management in CIDER
date: 2026-07-16 09:10 +0300
tags:
- Emacs
- Clojure
- CIDER
---

The [CIDER 2.0 announcement]({% post_url 2026-07-15-cider-2-0 %}) was, by
necessity, a mile wide and an inch deep - there was simply too much to cover.
So I'm planning to make up for that with a small series of articles, each
shedding a bit more light on one notable change and the reasoning behind it.
This is the first one, and it tackles the area that has probably generated more
confusion (and bug reports) than any other over the years: session management.

Truth be told, I meant to write this article back when the work landed in
[CIDER 1.22](https://github.com/clojure-emacs/cider/releases/tag/v1.22.0),[^1]
but I was so busy wrapping up CIDER 2.0 that I'm only getting to it now, as
part of this series. Better late than never, right?

## A bit of history

Back in the pre-history of CIDER (before 0.18), there were no sessions at all -
just *connections*, and a very simple rule: commands went to the most recently
used connection that made sense for your buffer. Crude? Sure. But it mostly
worked OK, and - crucially - everyone understood it.

CIDER 0.18 replaced this with [Sesman](https://github.com/vspinu/sesman)-based
session management. Sessions grouped related REPLs together and could be
*linked* to projects, directories and buffers, which made multi-project
workflows a lot more robust. Shortly afterwards, in 0.19, we introduced the
concept of *friendly sessions* (I wrote about it back in
[Happy New CIDER!]({% post_url 2019-01-01-happy-new-cider %})) - sessions that
CIDER would infer for buffers that had no explicit links, so that things Just
Worked without any setup.

I'll be honest: I was never a big fan of the friendly-session concept. It was
one more piece of terminology for users to learn ("linked? friendly? current?"),
and its behavior felt a bit like magic in some cases - your buffer would get
associated with *some* REPL, and it wasn't always obvious why that one. Magic is
great when it works and infuriating when it doesn't, and session inference
worked... most of the time.

## The breaking point

Over the years the friendly-session matching logic quietly accumulated
complexity. To decide whether a session was friendly to your buffer, CIDER
would check whether the file was on the session's *classpath* (fetched over
nREPL and rescanned regularly), and, failing that, try to match the buffer's
*namespace* against the namespaces loaded in the REPL. Clever! Also: slow,
hard to predict, and hard to debug.

Then it got worse. The matcher runs on a very hot path - the mode-line needs to
know the current REPL, so it effectively ran on **every redisplay**. At some
point a change made it call `file-truename` over every classpath root on each
of those runs, and Clojure buffers without a connected REPL became visibly
laggy ([#3933](https://github.com/clojure-emacs/cider/issues/3933) - if you've
ever felt CIDER make plain *editing* sluggish, that was probably it).

The performance bug was fixable in place. But while staring at the code I
realized the classpath scan never needed to be there at all. Friendly sessions
were [originally introduced](https://github.com/clojure-emacs/cider/issues/2446)
for essentially one use case: making `cider-find-var` (`M-.`) into a
dependency's source land in a buffer that still talks to your project's REPL.
That's a navigation problem, not an inference problem.

## The fix: less magic, more predictability

So [#3935](https://github.com/clojure-emacs/cider/pull/3935) restructured the
whole thing around two simple rules:

- When you jump to a dependency's source via a CIDER navigation command, the
  buffer gets *pinned* to the session you came from. Evaluation, completion and
  friends keep targeting that REPL, no inference required.
- For everything else, a session is friendly to a buffer simply when the
  buffer's file lives under the session's project directory. That's one cheap
  string comparison instead of a classpath scan.

That's the entire model now. It killed the redisplay lag, dropped a couple of
nREPL round-trips we used to make on every connection, and - more importantly
to me - you can now predict what CIDER will do without consulting the source
code.

Is the new behavior dumber than the old one? Slightly, and deliberately so.
It's still smarter than the pre-0.18 "last used connection" rule - project
boundaries are respected, and dependency buffers follow the session you were
working in. The one thing you lose is auto-association for files *outside* the
project that you open by non-CIDER means (say, a plain `find-file` into a jar).
For those you can link a session explicitly (`C-c C-s p` and friends) - or use
the next thing.

## Default sessions: opting out of inference entirely

While I was in the area, I also added (back) a *default session* facility -
old-timers may remember that the pre-sesman connection era had a similar
notion. If you'd rather have absolute predictability than any cleverness:

```
M-x cider-set-default-session
```

Pick a session, and from that point on *every* REPL lookup - evaluation,
completion, documentation, the lot - goes to it, regardless of which buffer
you're in. `M-x cider-clear-default-session` reverts to the normal
project-based association. No linking, no friendliness, no inference - just
"always use this one, until I say otherwise".

## The two styles, side by side

Say you're working on two projects at once - a backend and a frontend:

- `~/code/backend` with a Clojure REPL
- `~/code/frontend` with a shadow-cljs (ClojureScript) REPL

With the default (inference-based) behavior, things flow like you'd hope:

- You open `~/code/backend/src/api/handler.clj` and evaluate a form - it goes
  to the backend REPL, because the file lives under that session's project
  directory.
- You press `M-.` on a `ring` var and land in a jar - the dependency buffer is
  pinned to the backend session, so completion and evaluation keep working
  against the backend REPL.
- You switch to `~/code/frontend/src/ui/views.cljs` - commands now target the
  frontend REPL. No links, no configuration, no surprises.

Now the contrasting style. Suppose you're spending the afternoon debugging the
backend, but you keep bouncing between projects, scratch buffers, and random
files scattered around your disk, and you want *all* of it to hit the backend
REPL:

- `M-x cider-set-default-session RET backend`
- Every buffer - the frontend sources included - now talks to the backend REPL.
  What you evaluate is what you get, everywhere.
- When you're done: `M-x cider-clear-default-session`, and the project-based
  behavior above kicks back in.

The full dispatch pipeline (pinned buffer → default session → linked/friendly
session → REPL type filter → recency) is documented in detail in the
[session management docs](https://docs.cider.mx/cider/usage/managing_connections.html),
including an ASCII flowchart I'm unreasonably proud of. See in particular the
sections on [friendly sessions](https://docs.cider.mx/cider/usage/managing_connections.html#friendly-sessions)
and the [default session](https://docs.cider.mx/cider/usage/managing_connections.html#default-session).

## Closing thoughts

Session management is one of those areas where the "smart" solution and the
*good* solution turned out to be different things. Seven years of friendly
sessions taught me that users don't actually want their tools to be clever -
they want them to be predictable, and fast, and clever only when the cleverness
is cheap and explainable in one sentence. "Your project's files use your
project's REPL" passes that bar; "your buffer matches some session's classpath"
never did.

More posts about the notable changes in CIDER 2.0 are coming soon. Until then -
keep hacking!

[^1]: In retrospect these changes should have probably happened in 2.0, given
    that they altered some long-standing behavior. In my defense, when I was
    working on them I still hadn't decided whether 2.0 would be the next
    release or something in the more distant future.
