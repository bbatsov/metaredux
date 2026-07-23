---
layout: post
title: Leveling Up CIDER's ClojureScript Support
tags:
- Emacs
- Clojure
- ClojureScript
- CIDER
---

Continuing the series on the notable changes in
[CIDER 2.0]({% post_url 2026-07-15-cider-2-0 %}), let's talk about
ClojureScript - forever the trickier sibling in the CIDER family.

I'll start with a confession I've made before: I rarely use ClojureScript
myself, which is a big part of why its support in CIDER has historically lagged
behind Clojure's. Every "State of CIDER" survey reminds me of this, usually in
the comments section, occasionally in all caps. So in the 2.0 cycle I decided
to stop feeling vaguely guilty about it and actually do something - across
every layer of the stack: CIDER itself, `cider-nrepl`, and
[Piggieback](https://github.com/nrepl/piggieback).

## First, a strategic decision

The most important ClojureScript change in CIDER 2.0 isn't a feature - it's a
decision about what *not* to build. Some of CIDER's most powerful tools (the
debugger, enlighten, tracing, profiling) are deeply tied to JVM runtime
introspection, and porting them to ClojureScript would be a massive effort with
a poor cost/benefit ratio. Rather than keeping them in eternal "maybe someday"
limbo, we've explicitly scoped them as Clojure-only and focused the actual work
on the things cljs users hit every day: evaluation, testing, error reporting,
and clear behavior everywhere else.

That last part matters more than it sounds. Historically, invoking a
JVM-only command in a ClojureScript REPL would fail in some confusing way - a
cryptic error, a JVM-flavored result, or silence. Now the ops themselves report
a `clojure-only` status, and CIDER tells you plainly that the command isn't
supported for ClojureScript. Knowing what a tool *won't* do is half of trusting
it.

## What actually got better

- The regular test commands (`cider-test-run-ns-tests` and
  friends) now work in ClojureScript REPLs, asynchronous `cljs.test/async`
  tests included. Previously CIDER just refused, and you were stuck evaluating
  `(run-tests)` by hand like an animal.
- Expanding your own macros (e.g.
  ones brought in via `:refer-macros`) used to silently echo the form back
  unexpanded - a bug filed all the way back in
  [2017](https://github.com/clojure-emacs/cider/issues/2099). The compiler
  environment is now threaded to the analyzer properly, and it just works.
- `cider-nrepl` now resolves the
  ClojureScript compiler environment through a provider chain, with a dedicated
  shadow-cljs provider - so the static-analysis ops keep working in a shadow
  REPL that never loads Piggieback.
- The new `cider-tap` viewer works with ClojureScript
  too: a runtime helper buffers tapped values and the JVM side streams them to
  Emacs. (Tapped cljs values aren't inspectable - they live in the JS runtime -
  but you see them as they happen.)
- ClojureScript stack frames now render their
  `ns/fn` properly instead of degrading to `nil/nil`, and unqualified core vars
  resolve against `cljs.core` rather than falling back to `clojure.core`
  (which quietly broke things like indentation metadata).
- A recent ClojureScript on the classpath (whose
  Closure compiler wants JDK 21+) no longer crashes `cider-nrepl` at startup on
  an older JDK - you get a Clojure-only session instead of no session.
- Piggieback itself got a round of bug fixes in the
  0.6.x/0.7.0 releases - it's easy to forget it exists (which is rather the
  point of it), but it powers most cljs REPLs CIDER talks to.

The documentation kept pace too: the new
[full-stack Clojure + ClojureScript guide](https://docs.cider.mx/cider/cljs/up_and_running.html#full-stack-clojure-clojurescript-projects)
covers the two-REPLs-one-project setup that trips up nearly everyone, and the
[ClojureScript docs](https://docs.cider.mx/cider/cljs/overview.html) got a
general refresh.

## An unexpected assist

Fun aside: this is the area where AI coding agents helped me the most during
the 2.0 cycle. My ClojureScript experience is limited, but between the
excellent bug reports from the community and the ability to quickly prototype
and test fixes against real shadow-cljs and figwheel setups, problems that had
been "someone who knows cljs should look at this someday" for years finally got
fixed. Make of that what you will.

## What's next

I keep pondering some form of "native" shadow-cljs support, given that
shadow-cljs is what most ClojureScript users actually run these days. That's
still very much in the hammock phase, so don't hold me to it - but the
direction is clear: fewer moving parts, clearer errors, and honesty about
what's supported.

If you're a ClojureScript user, I'd genuinely love to hear how 2.0 feels in
your daily work - the feedback loop is what keeps this improving. Keep hacking!
