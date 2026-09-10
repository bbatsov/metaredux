---
layout: post
title: "Clojurists Together Update: July and August 2026"
date: 2026-09-10 12:30:00 +0300
categories: posts
tags:
- Clojure
- nREPL
- CIDER
- OSS
---

Time for another bi-monthly update on the work that [Clojurists Together](https://www.clojuriststogether.org/) are funding this year - my
maintenance of [nREPL](https://nrepl.org), [CIDER](https://cider.mx) and friends. I published the [previous one]({% post_url 2026-07-05-clojurists-together-update-may-june-2026 %}) as a blog post for the first time
and the feedback was good enough that I'll keep doing it.

Last time I said that I had plucked most of the low-hanging fruit and that the next two months were unlikely to be as productive. Well, I was wrong.
CIDER 2.0 finally shipped, and once it was out the door I used the momentum to sweep through pretty much every corner of the nREPL/CIDER ecosystem.
A few long-neglected projects got proper releases, and nREPL got a couple of brand new implementations in languages I play on the side from time to time.

<!--more-->

The big highlights from my perspective:

- CIDER 2.0 ("Terceira") is out, followed by 2.0.1, and 2.1 is taking shape on master
- [clj-refactor](https://github.com/clojure-emacs/clj-refactor.el) 4.0 is out
- [Sayid](https://github.com/clojure-emacs/sayid) went from 0.4 to 0.8 in the span of three weeks
- [Drawbridge](https://github.com/nrepl/drawbridge), nREPL's HTTP transport, got its first meaningful release in years
- nREPL went polyglot: [nREPL servers for Erlang and Elixir](https://github.com/nrepl/nrepl-beam) and [an OCaml client](https://github.com/nrepl/mezcaml)
- [clj-suitable](https://github.com/clojure-emacs/clj-suitable) 0.7 and 0.8 closed most of the gap between ClojureScript and Clojure completion
- A lot of work landed on nREPL's master (TLS hardening, URL-based connections, docs) and a new release is right around the corner

Below you'll find more details about the work I did, project by project.

## CIDER

[CIDER 2.0 ("Terceira")](https://github.com/clojure-emacs/cider/releases/tag/v2.0.0) landed on July 15, right on the schedule I had
announced in the [preview post]({% post_url 2026-06-30-cider-2-0-is-brewing %}). For once in my life I was actually on time!
The big themes were covered there and in the [release announcement]({% post_url 2026-07-15-cider-2-0 %}) (transient menus, inline macro stepping, call-graph browsers,
source-based find-references, the tracing and tap buffers, rich content in the REPL), so here's just what changed between
the preview and the final release:

- `cider-doctor`, which checks your Emacs setup and the active nREPL session for common problems and produces a report you can paste in a bug report
- an `orchard` value for `cider-print-fn`, selecting cider-nrepl's much faster `orchard.pp` pretty-printer
- SSH tunnels now forward a free local port, so remote REPLs sharing a port no longer collide on localhost
- `C-c C-d` at the stdin prompt sends end-of-input, and stdin is routed to the exact connection that asked for it
- a long tail of nREPL client fixes: a slow memory leak on the eldoc/completion path, `nrepl-dict-merge` mutating a shared literal, notifications treated as format strings

[CIDER 2.0.1](https://github.com/clojure-emacs/cider/releases/tag/v2.0.1) followed a week later with fixes for the problems early
adopters ran into: evaluation in a dependency's source buffer erroring with "No linked CIDER sessions" (in several variants),
`cider-enlighten-mode` never lighting anything up (a 1.22 regression), the macroexpansion commands refusing to expand
`let`/`fn`/`loop`, and `load-file` potentially freezing Emacs on a huge result. Nothing dramatic, but I'm glad people were quick to report those.

After that master (the future CIDER 2.1) kept moving at a steady pace. A few of the things that landed there:

- CIDER's dynamic font-locking (REPL-defined macros, functions, deprecated/instrumented/traced symbols) now works better in `clojure-ts-mode` buffers via tree-sitter. Previously it worked "officially" only under `clojure-mode`. The debugging reader tags are highlighted there too.
- A new `cider-preferred-clojure-mode` controls which mode CIDER uses to font-lock the code it renders - REPL results, doc examples, overlays and its own display buffers. `clojure-ts-mode` is finally a first-class citizen in CIDER.
- Symbol prompts can go through `completing-read` (so Vertico/Ivy/Helm kick in) and completion annotations render as an aligned type/namespace column in Corfu, Vertico and the built-in `*Completions*`. More on this in [Modernizing CIDER's Completion]({% post_url 2026-07-25-modernizing-cider-completion %}).
- Connecting got smarter. Container-published nREPL ports are resolved for `/docker:` and `/podman:` buffers, `lein trampoline` REPLs are detected, `.nrepl-port` files are no longer discarded on systems without `lsof`, and there's a new ["How CIDER Finds Ports"](https://docs.cider.mx/cider/basics/up_and_running.html#how-cider-finds-ports) section in the manual.
- Every form command got an "at point" variant (inspect, pprint, macroexpand, format, insert in REPL), there's a `cider-inspect-menu` listing every way to start an inspection, and the contents of `comment` forms are treated as top level by the whole defun command family.
- Stray output from long-lived background processes (say, a `core.async` go-loop still printing under a finished eval's id) is now routed to the REPL instead of being dropped with a warning.

One more thing. I shipped "smarter form targeting" on master - the evaluation commands resolving the form from where the cursor
actually is, rather than the form before it - [wrote about it]({% post_url 2026-08-26-smarter-form-targeting-is-coming-to-cider %}),
got a lot of feedback, and [reverted it]({% post_url 2026-08-29-smarter-form-targeting-is-not-coming-to-cider %}) a few days later.
CIDER 2.1 will keep the classic Emacs semantics. Fifteen years in, the existing behaviour is the contract, not an implementation detail I get to tidy up.
The detour wasn't wasted, though: it surfaced a bug where the text of a line comment was treated as code,
and the "at point" family of commands is a direct result of it.

## cider-nrepl

Three [cider-nrepl](https://github.com/clojure-emacs/cider-nrepl) releases in July, wrapping up the tools.deps migration and driving the CIDER 2.0 launch:

- [cider-nrepl 0.62.0](https://github.com/clojure-emacs/cider-nrepl/releases/tag/v0.62.0) finalized the Leiningen to tools.deps migration, simplified deferred middleware loading, documented the op response keys (with a test verifying the descriptor contract) and shipped the hardened content-type and slurp middleware that made rich content safe to enable by default.
- [cider-nrepl 0.62.1](https://github.com/clojure-emacs/cider-nrepl/releases/tag/v0.62.1) fixed a whole cluster of debugger bugs. Record literals no longer get downgraded to plain maps by instrumentation, `deftype`/`defrecord` method bodies are skipped (goodbye `Unable to resolve symbol: STATE__`), and enlightening `deftest` bodies works again.
- [cider-nrepl 0.62.2](https://github.com/clojure-emacs/cider-nrepl/releases/tag/v0.62.2) pruned trace and tap subscriptions with dead transports (a dead subscriber used to break every traced evaluation), stopped the debugger from shadowing enlighten's evaluator, brought the docs back in sync with the code and added a [section for tool authors](https://docs.cider.mx/cider-nrepl/tool-authors/index.html).

## Orchard

[Orchard 0.44.0](https://github.com/clojure-emacs/orchard/releases/tag/v0.44.0) shipped on July 4, mostly thanks to Sashko's inspector work (a `replace` command,
truncated table columns, `ARef` contents rendered fully). My part was a round of tests for the less covered namespaces and, later on master,
a fix for `orchard.print` ignoring custom `print-method` implementations for records and collections. Thanks, Sashko!

## clj-refactor 4.0

[clj-refactor.el 4.0](https://github.com/clojure-emacs/clj-refactor.el/releases/tag/v4.0.0) is the release I had been promising for a few cycles.
It requires Emacs 28.1+ and CIDER 2.0+, and it's a big one:

- project-wide refactorings (rename symbol, change signature, inline symbol) now show a diff preview before touching disk, and `cljr-undo-last-refactoring` reverts the last one in a single step
- the slow refactorings run asynchronously, so Emacs no longer freezes while the middleware analyzes the project
- `cljr-change-function-signature` can add and remove parameters and handles multi-arity functions
- a `clj-refactor-menu` transient replaces the hydra menus, and the `multiple-cursors`, `hydra` and `inflections` dependencies are gone
- many commands degrade gracefully without a REPL (`cljr-clean-ns`, `cljr-slash`, `cljr-add-missing-libspec`, `cljr-remove-let`, `cljr-promote-function`)
- `cljr-slash` can add and hotload a missing library, artifact lists are cached, and the namespaced refactor-nrepl ops are used when available

I still think the long-term home for the most useful bits is CIDER and clojure-mode, but at least the project is in good shape while I figure that out.
There's a bit more in the [release post]({% post_url 2026-07-16-clj-refactor-4-0 %}).

## clj-suitable

[clj-suitable](https://github.com/clojure-emacs/clj-suitable), the ClojureScript completion backend, was another project that had been coasting for years:

- [clj-suitable 0.7.0](https://github.com/clojure-emacs/clj-suitable/releases/tag/0.7.0) adapted to Piggieback 0.7's delegating repl-env, modernized every dependency, replaced the Leiningen build with tools.build, moved CI to GitHub Actions and added a shadow-cljs integration test over a real Node runtime.
- [clj-suitable 0.8.0](https://github.com/clojure-emacs/clj-suitable/releases/tag/0.8.0) brought the static completion much closer to compliment: fuzzy matching (`pr-fn` completes `print-function`), compliment-style ranking, completion of local bindings (destructuring included) and of referred vars inside `:refer` vectors. It also fixed the REPL's `*1`/`*2`/`*3` getting clobbered by completions and a few long-standing shadow-cljs and Node.js issues.

ClojureScript users - I'd love to hear how the new completion feels in practice.

## Sayid

The [Sayid revival]({% post_url 2026-07-01-sayid-redux %}) continued at a brisk pace, with five releases between July 1 and July 17:

- [Sayid 0.4.0](https://github.com/clojure-emacs/sayid/blob/master/CHANGELOG.md#040---2026-07-01) dropped the `com.billpiel` namespace prefix, added data-returning variants of the workspace and query ops, and introduced a client-rendered, foldable tree view of the recorded call tree built on CIDER's `cider-tree-view`.
- [Sayid 0.5.0](https://github.com/clojure-emacs/sayid/blob/master/CHANGELOG.md#050---2026-07-01) made recording bounded: a record limit, per-function limits, sampling, a max trace depth and bounded printing. Tracing a namespace under a test suite can't eat all your memory anymore.
- [Sayid 0.6.0](https://github.com/clojure-emacs/sayid/blob/master/CHANGELOG.md#060---2026-07-08) rebuilt inner tracing on `tools.analyzer.jvm`, replacing the fragile source-rewriting instrumenter.
- [Sayid 0.7.0](https://github.com/clojure-emacs/sayid/blob/master/CHANGELOG.md#070---2026-07-10) added `sayid.data` (the recorded call tree as plain data, with `tap>` integration for Portal and friends) and `sayid.golden`, a golden-trace testing helper.
- [Sayid 0.8.0](https://github.com/clojure-emacs/sayid/blob/master/CHANGELOG.md#080---2026-07-17) focused on the experience: a `sayid-menu` transient, plain-language feedback from the trace commands, getting-started hints in empty views, and a fix for the inspector integration that had been broken for years.

I wrote a bit more about the last one [here]({% post_url 2026-07-18-sayid-0-8 %}). Not bad for a project that was completely dead in June, right?

## Drawbridge

Drawbridge is nREPL's HTTP transport, created by Chas Emerick in 2012 and "technically maintained" ever since. I finally gave it the attention it needed:

- [Drawbridge 0.3.1](https://github.com/nrepl/drawbridge/releases/tag/v0.3.1) updated the dependencies (nREPL 1.7, Ring 1.15) and throttled client polling so it stops flooding servers with GET requests.
- [Drawbridge 0.4.0](https://github.com/nrepl/drawbridge/releases/tag/v0.4.0) is the interesting one. It adds `drawbridge.bridge`, a local nREPL socket server that relays to a remote Drawbridge endpoint, so any socket-based client (CIDER, Calva, rebel-readline) can now talk to Drawbridge. There's also a WebSocket transport with server push instead of long-polling, bearer-token authentication via `secure-ring-handler` (which refuses to run unauthenticated unless you insist), and a `deps.edn`, so it's usable as a git dependency.

The full story is in [Lowering the Drawbridge]({% post_url 2026-07-14-lowering-the-drawbridge %}).

## nREPL

No nREPL release this cycle, but master is shaping up nicely for 1.8:

- the built-in command-line client can connect using a URL, including the `nrepls://` and `nrepl+unix:` URLs that TLS and filesystem-socket servers advertise, and `http(s)://` when Drawbridge is on the classpath
- TLS hardening: descriptive errors for invalid key material, Ed25519 keys, tolerating a swapped certificate order, and a [documented security model](https://nrepl.org/nrepl/usage/tls.html)
- the built-in client sends input to the server as raw text, so reader typos, auto-resolved keywords and custom tagged literals no longer crash it
- stdin fixes: EOF arriving behind buffered input is reported properly, and a race between the stdin consumer and producer is gone
- `nrepl.spec` finally matches what `describe` and `ls-sessions` actually send
- a pile of documentation debt cleared (`lookup` return values, the `session-closed` status, the `-f`/`--repl-fn` option, [middleware best practices](https://nrepl.org/nrepl/building_middleware.html)) and a CI check keeping `ops.adoc` in sync with the descriptors
- Clojure 1.10 is the new minimum and `nrepl.misc/requiring-resolve` is gone in favour of the core one

The nrepl.org site also picked up links to several new clients and servers (Nautilos, nREPL.hx for Helix, Janet and Steel Scheme servers).
The nREPL family keeps growing, which makes me happy every single time.

## nREPL on the BEAM

[nrepl-beam](https://github.com/nrepl/nrepl-beam) is a brand new project I started in July, mostly because I wanted to see how well the nREPL spec holds up
when implemented from scratch outside the JVM. It's home to:

- `dialtone`, an nREPL server for Erlang (and a server core for the whole BEAM)
- `repartee`, the Elixir server built on top of it
- `chaser`, a terminal nREPL client that works with any nREPL server

[nrepl-beam 0.1.0](https://github.com/nrepl/nrepl-beam/releases/tag/v0.1.0) shipped on July 14. Both servers implement the full op set (eval with streamed output, sessions, interrupts, stdin, load-file, completions, lookup)
and pass [neat](https://github.com/nrepl/neat)'s cross-implementation integration suite alongside Clojure, Babashka and Basilisp.
Writing them was a good test of the spec, and it produced a few of the documentation fixes listed above. Turns out that the best way to find holes in a spec is to implement it in a language you barely know.

## mezcaml

In the same spirit, [mezcaml](https://github.com/nrepl/mezcaml) is a minimal nREPL client for OCaml: a small client library plus a command-line REPL,
working against any nREPL server regardless of the language on the other end. No release yet, but the core protocol works, it reads whole forms,
and it has server-driven completion. Nothing serious - it was a fun way to combine my recent OCaml hacking with nREPL.

## clojure-mode, clojure-ts-mode and MrAnderson

Smaller things: the `#_` toggle commands in clojure-mode were renamed to `clojure-toggle-discard` and friends (matching Clojure's own terminology, old names kept as aliases),
both modes got a `:to-have-face` matcher for font-lock tests, and clojure-ts-mode now checks the indentation of its sources on CI.

[MrAnderson 0.7.1](https://github.com/benedekfazekas/mranderson/releases/tag/v0.7.1) added a command-line interface, so it can be run without Leiningen,
and reworked its downstream integration tests against cider-nrepl and refactor-nrepl, which had silently stopped exercising local changes. Oops.

## Blog posts

I wrote a lot this summer, mostly a series on the notable changes in CIDER 2.0:

- [Lowering the Drawbridge]({% post_url 2026-07-14-lowering-the-drawbridge %})
- [CIDER 2.0: Sky is the Limit]({% post_url 2026-07-15-cider-2-0 %})
- [clj-refactor.el 4.0]({% post_url 2026-07-16-clj-refactor-4-0 %})
- [Simplifying Session Management in CIDER]({% post_url 2026-07-16-simplifying-session-management-in-cider %})
- [Stepping Through Macros in CIDER]({% post_url 2026-07-17-stepping-through-macros-in-cider %})
- [Sayid 0.8]({% post_url 2026-07-18-sayid-0-8 %})
- [clj-suitable 0.8.0: Closing the Gap with Compliment]({% post_url 2026-07-20-clj-suitable-0-8-0 %})
- [Making CIDER More Discoverable]({% post_url 2026-07-23-making-cider-more-discoverable %})
- [Modernizing CIDER's Completion]({% post_url 2026-07-25-modernizing-cider-completion %})
- [Closing the Find-Usages Gap in CIDER]({% post_url 2026-07-27-closing-the-find-usages-gap-in-cider %})
- [Sharpening CIDER's Debugging Tools]({% post_url 2026-07-28-sharpening-ciders-debugging-tools %})
- [Leveling Up CIDER's ClojureScript Support]({% post_url 2026-07-29-leveling-up-ciders-clojurescript-support %})
- [Smarter Form Targeting Is Coming to CIDER]({% post_url 2026-08-26-smarter-form-targeting-is-coming-to-cider %})
- [Smarter Form Targeting Is Not Coming to CIDER]({% post_url 2026-08-29-smarter-form-targeting-is-not-coming-to-cider %})

## Epilogue

Big thanks to Clojurists Together, Nubank and the other organizations and people supporting my Clojure OSS work! None of this would have happened without you. You rock!

As for what's next - CIDER 2.1 is the obvious milestone, and it's mostly a matter of letting the clojure-ts-mode integration settle.
After that I'd like to cut nREPL 1.8 with the TLS and URL work, and get mezcaml and the BEAM servers to a point where they are useful
to someone other than me. I won't make any predictions about productivity this time around. Clearly I'm bad at those.

Keep hacking!
