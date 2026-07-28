---
layout: post
title: Modernizing CIDER's Completion
date: 2026-07-25 09:00 +0300
categories: posts
tags:
- CIDER 2.0
- CIDER
- Clojure
- Emacs
- Completion
---

CIDER's code completion has quietly gotten quite good over the years, and I
don't think it gets enough credit. It's all built on Emacs's standard
`completion-at-point`, so it works with whatever completion UI you prefer -
the built-in one, [Corfu](https://github.com/minad/corfu),
[company](https://company-mode.github.io/) - without any special setup. Under
the hood [compliment](https://github.com/alexander-yakushev/compliment) does the
heavy lifting for Clojure (and
[clj-suitable](https://github.com/clojure-emacs/clj-suitable) for
ClojureScript), which means smart, backend-driven matching: `mai` completes to
`map-indexed`, `cji` to `clojure.java.io`, and an unimported `BiFun` to
`java.util.function.BiFunction`. The candidates come back ranked by the backend
and are context-aware - it knows when you're inside a `->` or completing a
`deftype` field.

Lately I've been giving the Emacs side of things some attention, to bring it in
line with the modern completion stack so many of us now use - Vertico, Corfu,
Consult, Marginalia and friends. Two changes are worth calling out.

The first is about the symbol prompts. A number of CIDER commands ask you for a
Clojure symbol when there's nothing at point - `cider-doc`, `cider-find-var`
and the like. Historically those prompts used the older completion machinery, so
your `completing-read` UI didn't kick in and you were left with `TAB` and a
`*Completions*` buffer. There's now `cider-use-completing-read-for-symbol` (off
by default for now); turn it on and those prompts go through `completing-read`
over a collection that queries the running REPL lazily as you type. Vertico,
Ivy, Helm - whatever you drive `completing-read` with - just works, and the
candidates carry their type and namespace.

The second is smaller, but I like it a lot: annotations now line up in a proper
column instead of trailing raggedly after each candidate.

![CIDER completion annotations](/assets/images/cider-completion-annotations.png)

This comes from an `affixation-function`, the richer successor to the old
`annotation-function`, so every frontend that understands it - the built-in
`*Completions*`, Corfu, Vertico - renders the aligned version. company keeps
showing its own trailing annotations, same as before.

Both changes are in the latest CIDER MELPA build and will ship in the next
stable release. As always, I'd love to hear how they work out for you.

That's all I have for you today. Keep hacking!

**P.S.** If you use Embark, I wrote up a fun way to act on Clojure symbols with
it - documentation, jump-to-definition, inspect and so on - over on
[Emacs Redux](https://emacsredux.com/blog/2026/07/25/cider-and-projectile-meet-embark/).
