---
layout: post
title: CIDER 2.0 is Brewing...
date: 2026-06-30 12:00 +0300
tags:
- Emacs
- Clojure
- CIDER
---

Normally I write these posts *after* a CIDER release is out, with the smug
satisfaction of someone who has already tagged the version and updated the
changelog. Today I'm flipping the script a little: the release isn't out yet,
but I'm too excited to keep quiet about it.

Here's the thing. The next CIDER release was supposed to be 1.23. But as I kept
piling change upon change, it slowly dawned on me that calling this "1.23" would
be doing it a disservice. So I'm now strongly considering shipping it as
**CIDER 2.0** instead.

## A different kind of release

The last few CIDER releases were on the conservative side - lots of important
internal work, but relatively little that you'd actually *notice* day to day. This one
is the exact opposite. It's stuffed with user-visible changes, many of which
have been sitting on the issue tracker and in the back of my mind for literally
years.

Why now? Two reasons.

First, all that boring groundwork paid off. Some of you noticed I spent a lot of
time recently backfilling tests and generally enriching the test suite. That
wasn't busywork - it was me building a safety net, so that finally making
sweeping changes would be safe and pleasant instead of terrifying. Mission
accomplished: I've been refactoring with abandon and sleeping just fine.

Second - and I know this is the controversial part - AI agents have been a
genuinely great help. Not for writing CIDER *for* me (the design taste is
still very much mine, thank you), but for quickly prototyping my half-formed
ideas in different ways so I can see and feel them before committing to
the approach I like best. Turns out "show me, don't tell me" works wonders when
the thing showing you can knock out a prototype in minutes. Boo me if you must,
but when you're the maintainer of so many projects and you have only so little
time to spend on all of them that really makes a difference.

There's a third ingredient worth mentioning: I'd been *hammocking* on most of
this for months, quietly turning the designs over in the back of my mind while I
was wrapping up [CIDER 1.22](https://github.com/clojure-emacs/cider/releases/tag/v1.22.0).
So while 1.22 itself took the better part of four months (and still shipped later
than I'd have liked), the release right after it has been snapping together in a
couple of weeks - the code was the easy part once the thinking was done. It helps
that a lot of these changes close issues that had been gathering dust for years;
the ClojureScript macroexpansion bug ([#2099](https://github.com/clojure-emacs/cider/issues/2099))
was filed all the way back in *2017*. Nothing beats hammock-driven development -
even if it's decidedly not the fastest way to ship anything.

OK, enough preamble. Let's look at the goodies.

## Keymaps you can actually discover

CIDER has *a lot* of commands, and historically the only way to find them was to
memorize cryptic key chords or grep the manual. No more. Every command prefix
now pops a [transient](https://github.com/magit/transient) menu (the same UI
magit made famous), and there's a top-level `cider-menu` that ties them all
together:

```
Evaluate             Code              REPL & session           Diagnostics
 e Eval...            t Test...         j Insert into REPL...    r Trace...
 m Macroexpand...     n Namespace...    x Jack-in / connect...   p Profile...
 d Documentation...   w References...                            l Log...
```

Crucially, this isn't a modal speed bump - your old muscle memory still fires
instantly. `C-c C-d C-d` runs `cider-doc` as fast as ever; the menu only shows
up if you *pause* after the prefix, wondering "wait, what else is in here?"

## One tree view to rule them all

A bunch of CIDER's browsers grew up independently and looked it. They now share
a single, foldable tree-view widget - the namespace browser, the spec browser,
and the new call-graph browsers all behave consistently (`TAB` to fold, `n`/`p`
to move around). For example, exploring who calls a function:

```
who-calls clojure.core/reduce
  ▾ my.app.core/sum
      ▸ my.app.api/handler
  ▸ my.app.util/total
```

Expand a node and CIDER lazily fetches the next level. It's a much nicer way to
spelunk through a codebase than a flat list of strings.

## Find usages, batteries included

Speaking of call graphs - CIDER now ships with genuinely capable cross-reference
functionality out of the box. `cider-who-calls`/`cider-who-is-called` for the
call graph, `cider-who-implements` for protocols and multimethods, plus
`xref-find-references` (`M-?`) that searches your *source* and therefore finds
usages even in code you haven't loaded into the REPL yet.

The practical upshot: if you were reaching for clj-refactor.el or clojure-lsp
*mostly to find usages*, you probably don't need them anymore. (If you were using
them for other things, carry on - we're friends, not rivals.)

Much of the inspiration here came straight from
[swank-clojure](https://github.com/technomancy/swank-clojure) and
[SLIME](https://github.com/slime/slime) itself, which have offered this kind of
cross-referencing for ages. Sometimes nothing beats revisiting the classics.

## The debugging toolbox got a serious polish

This is the part I'm most happy about. Pretty much every "what is my code
*actually* doing?" tool got some love.

The macro tooling is the headliner. The macroexpansion buffer finally grew a
header line that shows the active expander and options, cycles namespace display
and metadata in place, pulses the freshly-expanded form, and - at long last -
says something useful when you point it at a special form or an unresolved
symbol, instead of silently shrugging. Better still, there's a brand new inline
stepper (`cider-macrostep`, a Clojure spin on the venerable
[macrostep](https://github.com/emacsorphanage/macrostep) package) that expands
macros right where they sit, one step at a time. Expandable sub-forms get underlined so you can hop between them with
`n`/`p`, and every gensym is painted its own color, so you can finally follow
where that `g__1234` ends up. I did not expect macro debugging to be *fun*, and
yet here we are.

Tracing got the same treatment. Traced calls no longer smear themselves all over
your REPL output; they stream into a dedicated, foldable `*cider-trace*` buffer
instead:

```
*cider-trace*
▾ (my.app/process {:id 7})
    ▸ (my.app/validate {:id 7})  => true
    ▸ (my.app/persist {:id 7})   => {:id 7, :saved? true}
  => {:id 7, :saved? true}
```

And because "wait, what did I even trace?" is a question I ask myself
constantly, `cider-list-traced` and `cider-untrace-all` are now a keystroke
away.

Enlighten (you remember it, right?) picked up some manners too. You can light up
a single form with `cider-enlighten-defun-at-point` without flipping the global
mode, and `cider-enlighten-stop` makes the whole thing vanish at once, instead
of making you re-evaluate everything in penance. And for the `tap>` crowd,
`cider-tap` opens a buffer that streams whatever you send to `tap>` and lets you
crack any value open in the inspector with `RET`. It's `println` debugging,
minus the println guilt.

## ClojureScript gets some love too

ClojureScript is always the trickier sibling, but it got meaningful improvements
this round:

- You can now run ClojureScript tests with the regular test commands (including
  async `cljs.test` tests) instead of CIDER refusing to play along.
- Macroexpansion of *user-defined* cljs macros finally works (it used to silently
  echo the form back unexpanded - a bug that had been open since forever).
- And when you invoke a Clojure-only command under a cljs REPL, CIDER now tells
  you so clearly, rather than failing in some confusing way.

This was another area where AI tooling was quite helpful to me, as I've rarely
used ClojureScript in the past, but I still managed to figure out how to finally
solve those problems that have been pain points for CIDER's users for as long as
we've had ClojureScript support.

On a related note, I've also been chasing down a few small bugs in
[Piggieback](https://github.com/nrepl/piggieback) (the middleware that powers
most of the cljs REPLs CIDER talks to) - have a look at the recent
[releases](https://github.com/nrepl/piggieback/releases) if you're curious. And
I've been idly pondering some form of "native" shadow-cljs support down the road.
That last one is very much TBD, so don't hold me to it - but the ClojureScript
story keeps inching forward.

## A pile of quality-of-life touches

It wouldn't be a CIDER release without a long tail of small comforts. A few that
I keep bumping into and grinning at:

- Eldoc is asynchronous now, so dragging the cursor around no longer blocks Emacs
  on an nREPL round-trip. Buttery smooth.
- A new `cider-mode` lighter (with an optional fringe marker) flags when the
  current buffer's namespace isn't loaded into the REPL, or has gone stale
  because you edited an already-evaluated form. That retires a whole genre of
  "why isn't my change taking effect?" head-scratching.
- `cider-doc` can pull ClojureDocs examples right into the doc buffer.
- Sending a form into your namespace's `(comment ...)` block is a one-keystroke
  affair now (`cider-send-to-comment`), and `cider-jump-to-comment` teleports you
  back there.
- Stuck in `.cljc` land? You can pin where a buffer's evaluations go - clj, cljs,
  or both - with `cider-set-eval-destination` and friends.
- The REPL banner is slimmer and far less shouty; the getting-started spiel now
  lives in a summonable reference card (`C-c C-h`).
- The cheatsheet finally learned about all the functions Clojure has grown since
  1.11.

I'm probably *still* forgetting a dozen things - the
[changelog](https://github.com/clojure-emacs/cider/blob/master/CHANGELOG.md) is
genuinely enormous this time around.

## Please go play with it!

> Unfortunately no one can be told what CIDER 2.0 is -- you have to experience it
> yourselves...
>
> -- Clorpheus

Here's where you come in. All of this is already available in the **snapshot
release of CIDER on [MELPA](https://melpa.org/#/cider)**. I'd love for you to
install it, kick the tires, and - especially - tell me how the various UX
changes feel. Discoverable? Annoying? Joyful? I genuinely want to know before I
carve any of it in stone.

If no serious problems surface, I plan to cut the real release fairly soon -
think a week or two. So now's the perfect time to influence it.

## Epilogue

I keep hearing that the Clojure community isn't innovating much these days. I
hope CIDER 2.0 goes a small way towards convincing the doubters that we're not
quite done yet. The best is always yet to come - for both Clojure *and* Emacs.

Now let's go forth and brew some (magic) CIDER together! Keep brewing!
