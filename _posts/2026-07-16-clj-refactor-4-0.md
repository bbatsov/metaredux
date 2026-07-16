---
layout: post
title: clj-refactor.el 4.0
date: 2026-07-16 18:57 +0300
tags:
- Clojure
- Emacs
- nREPL
---

Hot on the heels of [CIDER 2.0]({% post_url 2026-07-15-cider-2-0 %}),
[clj-refactor.el 4.0][release] is out! It's the first major release of the
project in almost five years, and this time around the version bump is not
just ceremonial - 4.0 is the biggest batch of user-facing improvements
clj-refactor has seen in ages, plus a healthy dose of long-overdue spring
cleaning.

<!--more-->

## Unfinished business

When I announced [clj-refactor.el 3.0]({% post_url 2021-10-26-clj-refactor-3-0 %})
back in 2021, I opened with a confession - we had failed to deliver the main
objective of that release, namely restoring the dependency hot-loading that
JDK 9's module system broke. Well, better late than never:
`cljr-hotload-dependency` is back in 4.0, reimplemented on top of
`clojure.tools.deps`, and it even accepts `deps.edn`-style coordinate maps
alongside the classic Leiningen vectors.[^1]

I'll also be honest about the bigger picture, as I was back then:
clj-refactor spent most of the intervening years in maintenance mode, while
`clojure-lsp` became the default refactoring engine for much of the community.
That hasn't discouraged me, though - it clarified what clj-refactor should
be. The project now has a proper [roadmap][roadmap] that spells out where
things are headed (spoiler: a static analysis index and closer ties with
CIDER), and 4.0 is the first big step in that direction.

## The highlights

The [changelog][changelog] for 4.0 has over 40 entries, but here's what I'm
most excited about:

- There's a new `clj-refactor-menu` - a `transient` menu of every command,
  grouped by category (bound to `hh` under your prefix, e.g. `C-c C-m hh`).
  It mirrors the classic two-letter mnemonics, so it doubles as a way to
  finally learn them. It replaces the old hydra-powered menus, which allowed
  us to drop the `hydra` dependency.
- Project-wide refactorings are no longer a leap of faith. `cljr-rename-symbol`,
  `cljr-change-function-signature` and `cljr-inline-symbol` now show you their
  edits as a diff and touch nothing until you confirm, and the new
  `cljr-undo-last-refactoring` (`ur`) reverts the last applied refactoring
  across every affected file in one step.
- The heavyweight commands went asynchronous. Renames, signature changes and
  file renames used to freeze Emacs while refactor-nrepl analyzed the project -
  now they return immediately, show a spinner in the mode-line, and finish when
  the analysis does. Cold-cache runs on big projects are dramatically more
  pleasant.
- `cljr-change-function-signature` grew up: it can add and remove parameters
  now (not just reorder and rename them), it understands multi-arity
  functions, and a long-standing parsing bug that broke it against recent
  CIDER releases is fixed.
- `cljr-slash` (the magic requires feature) learned a neat trick: entries in
  `cljr-magic-require-namespaces` can carry an `:artifact` coordinate, and
  when the namespace isn't on your classpath, typing `json/` will offer to add
  the library to your project and hotload it on the spot.
- A bunch of commands now work without a running REPL. `cljr-slash` and
  `cljr-add-missing-libspec` fall back to the static alias table,
  `cljr-clean-ns` falls back to sorting the ns form, and `cljr-remove-let` and
  the `#(...)` -> `(fn ...)` promotion are handled purely on the Emacs side.
  Refactoring your requires before the REPL is up is no longer a paper cut.
- Everything got snappier. Artifact lists and libspec suggestions are cached
  client-side (the latter matters because `cljr-slash` fires on every `/`
  keystroke), and the startup cache warming now actually populates the cache
  the interactive commands read from.
- clj-refactor now talks to refactor-nrepl using namespaced op names
  (`refactor/clean-ns` and friends), with a graceful fallback for older
  middleware versions. The companion [refactor-nrepl][refactor-nrepl] 3.14.0
  release also hardened the AST analysis and made find-usages noticeably
  faster on projects with many macros.
- The dependency diet continued: `multiple-cursors`, `hydra` and `inflections`
  are gone, along with a pile of aliases that had been deprecated since 2.3.0.
  clj-refactor is a much leaner package than it used to be.
- And a long tail of quality-of-life fixes - `cljr-describe-refactoring` now
  opens the wiki page in your browser instead of scraping HTML,
  the dependency commands give clear errors instead of raw search failures,
  and `clojure-ts-mode` users get a working offline ns sort and proper setup
  docs.

## Upgrading

4.0 is a major release and it does come with breaking changes: the minimum
requirements are now Emacs 28.1 and CIDER 2.0, the long-deprecated 2.3.0-era
command aliases are gone (use the `clojure-*` equivalents that live in
`clojure-mode` these days), and the legacy `namespace-aliases` code path in
`cljr-slash` was removed along with its three configuration options. On the
middleware side you don't need to do anything if you use `cider-jack-in` -
refactor-nrepl 3.14.0 gets injected automatically, and older versions keep
working thanks to the op-name fallback. Most setups should upgrade without
touching a thing, but do skim the [changelog][changelog] if you have a
heavily customized configuration.

## Five years later

Reading the 3.0 announcement again, I wrote back then that we wanted to show
"we haven't reached the limit of what one can do with a purely (mostly?)
REPL-powered approach to refactoring". I still believe that, and 4.0 is
hopefully some evidence. The [roadmap][roadmap] has the rest of the plan -
including making the analysis fast and robust enough to erase the reasons
people reach for other tools - and with CIDER 2.0 out the door I finally have
some cycles to spend here.

Huge thanks to everyone who contributed to clj-refactor and refactor-nrepl
over the years, to [Clojurists Together](https://www.clojuriststogether.org/)
for supporting the broader CIDER ecosystem, and to everyone who kept filing
issues during the quiet years - they shaped most of what shipped in this
release.

In the REPL we trust!

[release]: https://github.com/clojure-emacs/clj-refactor.el/releases/tag/v4.0.0
[changelog]: https://github.com/clojure-emacs/clj-refactor.el/blob/master/CHANGELOG.md
[refactor-nrepl]: https://github.com/clojure-emacs/refactor-nrepl/releases/tag/v3.14.0
[roadmap]: https://github.com/clojure-emacs/clj-refactor.el/blob/master/ROADMAP.md

[^1]: Only five years late. In my defense, nobody said *when* it would be
    restored.
