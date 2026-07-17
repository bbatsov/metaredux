---
layout: post
title: Stepping Through Macros in CIDER
date: 2026-07-17 08:15 +0300
tags:
- Emacs
- Clojure
- CIDER
---

This is another installment in the series of articles about the notable changes
in [CIDER 2.0]({% post_url 2026-07-15-cider-2-0 %}). Today's topic is one of
those "ambitious ideas that lay dormant for ages" I keep mentioning: proper
interactive macro stepping.

## The Dream

CIDER has had macroexpansion support practically forever - `C-c C-m` expands
the form before point into a dedicated buffer, a feature we inherited
spiritually from SLIME. It works, but it has always felt a bit... detached. The
expansion lives in another buffer, divorced from the code you're reading, and
for deeply nested macros you end up bouncing between buffers trying to keep
your bearings.

Emacs Lisp hackers have long had something nicer:
[macrostep](https://github.com/emacsorphanage/macrostep), a brilliant little
package that expands macros *in place* - right where they sit in your code - one
step at a time, and collapses them back when you're done. I've wanted a Clojure
version of this for years. CIDER 2.0 finally ships one.

## Standing on shoulders, not on top of them

I'm hardly the first person to want this. The idea of bridging CIDER and
macrostep goes back to at least
[2016](https://github.com/clojure-emacs/cider/issues/1850), where a proof of
concept wired up macrostep's extension hooks to CIDER by injecting a couple of
helper functions into your REPL and shuttling forms back and forth as strings.
Later, [macrostep-geiser](https://github.com/nbfalcon/macrostep-geiser) - a
Scheme-oriented macrostep backend - grew CIDER support as well, and SLIME
itself ships a `slime-macrostep` contrib built on the same extension API. So
the hooks were there, the hacks existed, and I could have blessed one of them
and called it a day.

I opted for a clean, from-scratch implementation in CIDER instead - mostly to
provide the best possible experience for Clojure programmers, without the
compromises the bridges had to make. macrostep's extension API was designed
around Emacs Lisp's happy circumstances, and Clojure violates most of them:

- `macrostep-expand-1-function` must be *synchronous*. That's fine when
  expansion is an elisp function call; it's less fine when it's a network
  round-trip to an nREPL server. (CIDER has been busy removing exactly this
  kind of blocking call lately - asynchronous eldoc being the poster child.)
- The API traffics in *forms* - elisp data structures. But Clojure code isn't
  elisp data: keywords, reader tags, `#()` lambdas and namespaced maps have no
  faithful elisp representation, so every bridge ends up round-tripping code
  through strings and hoping the quoting survives. The 2016 proof of concept
  literally did `(format "(expand-once '%s)" form)`, which should make anyone
  a little nervous.
- `macrostep-print-function` expects the *client* to print and fontify the
  expansion. In CIDER the *server* does the printing - that's how we get
  namespace tidying (`when` instead of `clojure.core/when`), print-option
  handling and metadata display for free. A client-side printer would have to
  reimplement all of that, badly.

There's also a less technical reason: lately I've grown rather averse to
adding third-party dependencies to CIDER. A dependency is a bet on someone
else's continued enthusiasm, and such bets sometimes go bad - tellingly,
macrostep itself spent a stretch unmaintained and now lives in a GitHub org
literally named "emacsorphanage" (it has since found a new maintainer, but the
point stands). CIDER is 14 years old and intends to stick around; over that
kind of horizon, owning ~600 lines of overlay code is cheaper than adopting
someone else's semi-abandoned package. So we kept
macrostep's brilliant UX ideas and its familiar keybindings, and left the
coupling behind.

## cider-macrostep

The entry point is `cider-macrostep-expand` (`C-c M-m e`). Put point after a
macro form, invoke it, and the form is replaced inline with its one-step
expansion, highlighted so you can tell what's expansion and what's your code:

```clojure
(when-let [x (fetch-thing)]
  (process x))

;; C-c M-m e =>

(let [temp__5825__auto__ (fetch-thing)]
  (when temp__5825__auto__
    (let [x temp__5825__auto__]
      (process x))))
```

From there you're in `cider-macrostep-mode`, where the further-expandable
sub-forms are underlined, `n`/`p` hop between them, `e` (or `RET`) steps into
one, and `c`/`q` collapse one level or everything back to the original code.
Your buffer is never
actually modified in a way that sticks - collapse everything and it's exactly as
it was.

A couple of touches I'm particularly fond of:

- Every distinct gensym in the expansion gets its own color, so you can finally
  track where that `temp__5825__auto__` flows through the expanded code. Once
  you've seen a `for` expansion with colorized gensyms, you won't want to go
  back.
- `E` (`cider-macrostep-expand-all`) fully expands the form in one step, for
  when you don't care about the journey.
- `b` (`cider-macrostep-expand-in-buffer`) runs the same stepping session in a
  dedicated popup, leaving the source buffer untouched - handy when you're in
  someone else's code and feel uneasy about inline rewrites, however temporary.

## The classic buffer got some love too

The traditional macroexpansion buffer wasn't neglected either: it grew a header
line showing the active expander and display options, `n` and `t` cycle
namespace display and metadata in place, `g` re-expands with the latest macro
definition, and freshly expanded forms pulse briefly so your eye lands in the
right place.

The expansion commands also got more talkative: pointing them at an unresolved
symbol now tells you whether the namespace simply isn't loaded yet (evaluate
the buffer!) or you've got a typo, instead of silently doing nothing.[^1]

## Why bother?

Macros are the part of Clojure people are most likely to describe as "magic",
and the standard advice - "just macroexpand it" - has always carried a hidden
tax: the expansion of any non-trivial macro is a wall of gensyms and nested
`let*`s that's genuinely hard to read cold. Stepping through the expansion one
level at a time, in place, with the gensyms color-coded, turns that wall into
something you can actually follow. I did not expect macro debugging to become
*fun*, and yet here we are.

All the details are in the
[macroexpansion docs](https://docs.cider.mx/cider/debugging/macroexpansion.html).
Give it a try the next time a macro surprises you - and keep hacking!

[^1]: Amusingly, we initially overdid these diagnostics - the guard refused to
    expand `let` and `fn` (which are macros wearing special-form badges) and
    broke a beloved trick of using macroexpansion to normalize reader syntax
    like `::auto/keywords`. See
    [#4111](https://github.com/clojure-emacs/cider/issues/4111) - fixed right
    after 2.0. Even diagnostics need diagnostics.
