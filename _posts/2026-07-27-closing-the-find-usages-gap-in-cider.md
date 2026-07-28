---
layout: post
title: Closing the Find-Usages Gap in CIDER
date: 2026-07-27 10:30 +0300
tags:
- CIDER 2.0
- Emacs
- Clojure
- CIDER
---

Next up in the series on the notable changes in
[CIDER 2.0]({% post_url 2026-07-15-cider-2-0 %}): cross-references. Or, as
most people call the feature, "find usages" - for years the most commonly
cited reason to run [clojure-lsp](https://clojure-lsp.io/) alongside (or
instead of) CIDER. Let's talk about why that gap existed and how we finally
closed it.

## Why runtime xref wasn't enough

CIDER has had *runtime* cross-referencing for a while: the `cider/fn-refs` op
walks the loaded vars in your REPL and reports which functions reference the
one at point. It's a genuinely cool trick - the REPL literally knows your
program - but as a "find usages" answer it has three structural problems:

- It only sees **loaded** code. Namespaces you haven't required yet - often
  most of the codebase - are invisible.
- It reports *functions*, not *occurrences*. Each hit points at the caller's
  definition, not the exact call site, and a function that calls yours three
  times shows up once.
- It's JVM-only, so ClojureScript users got nothing.

clojure-lsp, by contrast, builds a static index of your whole project with
clj-kondo's analyzer and answers instantly, loaded or not. For
occurrence-oriented questions ("show me every place this is used, so I can
change all of them"), static analysis is simply the right tool. No amount of
runtime cleverness fixes "the code isn't loaded".

## The fix: search the source

So CIDER 2.0 does the obvious thing we should have done years ago:
`xref-find-references` (`M-?`) now finds references by **searching the
project's source files** on disk. Unloaded code, `cljs` files, commented-out
drafts - if the name occurs in the project, you'll see the exact occurrence,
in the standard xref UI you already use for everything else in Emacs. To
borrow the franchise that has been handing programmers debugging metaphors
for over two decades now: the runtime is the Matrix, a tidy compiled illusion
of your program, and to see every place a thing is really used you sometimes
have to unplug and look at the source itself.[^matrix]

Here's the difference in one picture - the same query on
`orchard.misc/require-and-resolve`, first in runtime mode, then in source
mode:

![Find-usages of orchard.misc/require-and-resolve: the runtime search returns only the loaded callers, one per calling function, while the source search finds every occurrence across the project - including the alias-qualified uses in other namespaces and a cljc file the REPL never loaded](/assets/images/cider-xref-runtime-vs-source.gif)

The runtime knows about three callers - and points you at each *caller's*
definition. The source scan turns up all ten actual occurrences, across five
files, including the ones written as `misc/require-and-resolve` in namespaces
the REPL never loaded. That's the gap, in one screenshot.

Now, "search the source" makes it sound like a `grep`, and I want to be clear
that it isn't a dumb one. Say you're chasing `orchard.misc/require-and-resolve`.
The search runs in three stages:

1. First CIDER asks the REPL to resolve the symbol at point to its fully
   qualified name, `orchard.misc/require-and-resolve`. The REPL is right there
   on the other end of the wire, so why guess when you can ask?
2. A fast first pass (`ripgrep`, via Emacs' own `xref-matches-in-files`) finds
   every file that so much as mentions `require-and-resolve`, purely to narrow
   the field.
3. Then the Clojure-aware part. For each of those files, CIDER reads its
   `(ns ...)` form to learn *how* that file pulls in `orchard.misc` - aliased
   as `[orchard.misc :as misc]`, brought in with `:refer [require-and-resolve]`,
   or is this `orchard.misc` itself? - and builds a regexp that matches only
   the forms that file could legitimately use: the qualified
   `orchard.misc/require-and-resolve`, the aliased `misc/require-and-resolve`,
   or a bare `require-and-resolve` where the namespace declaration makes that
   valid. The requires inside the `ns` form are excluded, so the import line
   doesn't show up as a "usage".[^fix]

Is any of this as smart as clj-kondo's full analysis? No - it's ultimately a
syntactic search, so an identically named var in another namespace can still
sneak through as a false positive. But because it's ns-aware rather than a
blind text match, a bare `require-and-resolve` in some file that never requires
`orchard.misc` (and has a `require-and-resolve` of its own) won't be mistaken
for yours. For the daily "where is this used?" question it turns out to be
remarkably close to the real thing in practice, it requires zero extra
infrastructure, and it composes with what only CIDER has: the running REPL.

That composition is configurable via `cider-xref-references-mode`:

- `source` (the default) - occurrences from the project's files.
- `runtime` - the historical loaded-vars behavior.
- `both` - source occurrences first, plus the runtime hits the scan can't see.
  And there *are* such hits: references generated by macro expansion leave no
  textual trace in your source, but the runtime knows about them. Static
  analysis can't ever tell you those; your REPL can.

(There's also `cider-xref-fn-refs-in-source`, `C-c C-? s`, when you want the
source search explicitly, and outside a project the source mode gracefully
falls back to the runtime search.)

## Beyond find usages: the who-* family

While closing the gap, we went further and built out a whole family of
SLIME-inspired cross-referencing commands under `C-c C-w`, most of them
rendered as expandable trees:

- `cider-who-calls` / `cider-who-is-called` - the call graph, upward and
  downward. Expand a caller to see *its* callers; spelunk as deep as you like:

  ![The cider-who-calls tree, expanded two levels up the call graph](/assets/images/cider-who-calls-tree.png)

- `cider-who-implements` - a protocol's implementing types (inline
  `defrecord`/`deftype` implementations included) or a multimethod's dispatch
  values, each jumping to the implementation's source. Multimethods are a nice
  case study in hybrid thinking: the method functions carry no source metadata
  at runtime, so CIDER locates the `defmethod` forms by - you guessed it -
  searching the source.
- `cider-type-protocols` / `cider-protocols-with-method` - the reverse
  lookups: what does this type implement, and which protocols declare this
  method?
- `cider-who-macroexpands` - a macro's use sites, found via source search,
  because macro invocations are expanded away at compile time and the runtime
  literally cannot see them.

Much of this is powered by new ops in `cider-nrepl` (and
[Orchard](https://github.com/clojure-emacs/orchard) underneath), and much of
the inspiration came straight from SLIME and swank-clojure, which offered
`who-calls` back when Clojure itself was barely out of the crib. And here's
the part I love: the whole "go read the source instead of trusting the
runtime" instinct was already there in swank-clojure's implementation. Its
`who-calls` would find candidate callers among the loaded vars, sure, but then
it went and *read the actual source form of each one off disk* and walked the
parsed code looking for your symbol, rather than believing whatever the
compiled runtime claimed. It was still anchored to loaded code - it never
scanned the whole project the way CIDER 2.0 does - but the core idea, that the
source on disk is the ground truth and the runtime is just a convenient
approximation, predates this release by about fifteen years. Good ideas don't
expire. Sometimes nothing beats revisiting the classics.[^source]

## So do you still need clojure-lsp?

If you were running clojure-lsp *primarily* for find-usages - the most common
answer I heard when I asked - then CIDER now covers you out of the box. If you
use it for project-wide renames, unused-var linting, or editing without a REPL,
carry on; those are real strengths of static analysis and CIDER doesn't try to
replicate them. The two continue to
[work fine side by side](https://docs.cider.mx/cider/config/lsp.html), and the
new async eldoc even yields politely so LSP-provided docs can compose with
CIDER's.

My goal was never to "beat" clojure-lsp - it was to make a freshly installed
CIDER answer the questions every Clojure programmer asks a dozen times a day,
with no extra moving parts, and with the one advantage nobody else has: a live
runtime on the other end of the wire.

The full story is in the
[navigation docs](https://docs.cider.mx/cider/usage/navigation.html). Keep
hacking!

[^matrix]: As Morpheus puts it: "Unfortunately, no one can be told what the
    Matrix is. You have to see it for yourself." Same with find-usages,
    really - I can tell you a var is used in seven places, but until you've
    seen the actual call sites you don't really know what changing it will
    break.

[^source]: "The path of the One ends at the Source." The Architect was
    talking about Zion, but he might as well have been describing every
    debugging session that ends with you finally opening the file and reading
    the code instead of theorizing about it.

[^fix]: Full disclosure: matching the aliased and namespace-qualified forms
    correctly only landed *after* 2.0 - the 2.0.x releases had a bug where the
    source scan quietly skipped files that referenced a var through its alias,
    which is of course the common case. The fix will ship in CIDER 2.1, which
    doesn't have a release date yet. I'm hoping to get back into a rhythm of
    cutting a new CIDER release every month or two, so it shouldn't be a long
    wait.
