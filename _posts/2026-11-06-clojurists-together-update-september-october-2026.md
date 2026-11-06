---
layout: post
title: "Clojurists Together Update: September and October 2026"
date: 2026-11-06 11:00:00 +0200
categories: posts
tags:
- Clojure
- nREPL
- CIDER
- OSS
---

Time for another bi-monthly update on the work that [Clojurists Together](https://www.clojuriststogether.org/) are funding this year - my
maintenance of [nREPL](https://nrepl.org), [CIDER](https://cider.mx) and friends. You can find the [previous one]({% post_url 2026-09-10-clojurists-together-update-july-august-2026 %}) here.

For a change, most of my focus in September and October was on nREPL itself, not on CIDER.
The big themes were the nREPL protocol spec and how well the servers and clients out there actually stick to it.
For years building an nREPL server or client that plays well with the rest of the ecosystem came down to trial and error,
and this time I finally did something about it. It paid off much sooner than I expected.

<!--more-->

The big highlights from my perspective:

- [proof](https://github.com/nrepl/proof), a compatibility suite for nREPL servers and clients, is out, and it's already found (and helped fix) problems in nREPL itself, Babashka, CIDER, Calva, Conjure and many other servers and clients
- [neorepl](https://github.com/nrepl/neorepl), a brand new terminal REPL meant to be something like "CIDER in a terminal" and "CIDER for agents", went from 0.1 to 0.4 in two weeks
- nREPL 1.8 is out, and master already has fixes for some of the bugs proof found in it
- [Piggieback](https://github.com/nrepl/piggieback) 0.7.1 and 0.8.0 fixed a few long-standing problems with ClojureScript REPLs
- nrepl.org became a proper landing page (with an nREPL server running right in your browser), the manual moved to docs.nrepl.org, and CIDER got a landing page of its own at [cider.mx](https://cider.mx)
- CIDER's master gained streaming test results and commands for editing the `ns` form, backed by new cider-nrepl and Orchard releases

Below you'll find the details, project by project.

## proof and the nREPL spec

[proof](https://github.com/nrepl/proof) is the big new project this time around, and the one I'm most excited about.
I've been talking about some automated way to check nREPL compatibility for ages (it made my list of [things people hate about nREPL]({% post_url 2023-03-26-bad-nrepl %}) back in 2023),
so it feels really great to finally see it become a reality. Good things take a while to happen, they said...
Does your server work with CIDER? Until now the only way to find out was to implement the protocol, connect CIDER (or Calva, or whatever your users run)
and see what breaks. Now you can point proof at your server and it'll tell you. It's a single binary that runs about 80 checks against a server.
A check fails only when a real client would break (with a link to the client code that depends on the behavior), and everything else is just a warning.
proof also comes with client profiles that send exactly what CIDER, Calva, Conjure, vim-fireplace, REPLy and neorepl send,
profiles for the servers I know of, and a GitHub Action for running it in a server's CI.

Once the server side was in place, I started on the clients as well. `proof proxy` sits between a client and a server and checks what the client sends,
and `proof serve` is a small server for the test suites of clients, which can mimic the quirks of nREPL, Babashka, jank and others (`proof serve -like jank`).
I wrote about proof in a lot more detail in [proof: How Strong Is Your nREPL?]({% post_url 2026-11-05-proof-how-strong-is-your-nrepl %}).

- [proof 0.1.0](https://github.com/nrepl/proof/releases/tag/v0.1.0) came out on October 26.
- [proof 0.2.0](https://github.com/nrepl/proof/releases/tag/v0.2.0) followed a week later, along with a [compatibility matrix](https://nrepl.github.io/proof/) that runs proof against every server it knows about daily.

proof goes hand in hand with the [nREPL spec](https://spec.nrepl.org), the draft Phil Hagelberg wrote earlier this year.
When the spec and the existing clients disagree, proof goes with the clients (a spec that breaks CIDER isn't much use to anyone),
and every such conflict, along with the gaps proof ran into, ends up on a [list of spec changes](https://github.com/nrepl/proof/blob/main/doc/spec-changes.md).
The first few of them are already PRs against the spec: how replies relate to their requests, the status values, how interrupts are answered
and the fields of lookup replies.

What I like the most about proof is how quickly it started paying off:

- nREPL itself sent the `done` of an eval interrupted from another connection to the wrong connection (fixed on master), drops the evals waiting behind an interrupted one ([#503](https://github.com/nrepl/nrepl/issues/503)) and can't interrupt an eval that hasn't started yet ([#504](https://github.com/nrepl/nrepl/issues/504)). Not great!
- Between proof and neorepl, Babashka got a couple of dozen issue reports from me in October, from test ops that never answered to a session that stopped answering after its client went away in the middle of an eval. Michiel Borkent fixed nearly all of them the same day or the next. You rock, Michiel!
- CIDER didn't finish setting up connections to servers without cider-nrepl (fixed), Calva and Conjure didn't sort the keys of the dictionaries they sent (both fixed within days), vim-fireplace and REPLy never close their sessions, and REPLy's and Conjure's interrupts don't say which eval to stop.
- Basilisp and jank got a few reports as well (lookup replies, and a request that throws taking its whole connection down).
- My own toy implementations (nrepl-beam, mezcaml and neat) were the first projects I ran proof against (more on them below).

I was also super happy to see other people using proof already. Tom Waddington has been running it against his nREPL implementations
for [Janet](https://github.com/waddie/nrepl-janet) and [Steel Scheme](https://github.com/waddie/nrepl-steel) since early October (nrepl-janet even tests its client with `proof serve`),
Jeaye Wilkerson [suggested](https://github.com/jank-lang/jank/issues/1104) using it for jank's nREPL server, and Kyle Cesare is already [fixing](https://github.com/jank-lang/jank/pull/1175) what it found there.
That's exactly what I was hoping for!

## neorepl

[neorepl](https://github.com/nrepl/neorepl) is the other brand new project, and it grew up together with proof.
It's a [terminal REPL for Clojure (and anything else that runs an nREPL server)]({% post_url 2026-10-10-neorepl-a-terminal-repl-for-clojure-and-friends %}),
and the best way I can describe it is "CIDER in a terminal": it uses cider-nrepl when it's around, the same way CIDER does,
and falls back to nREPL's built-in ops when it isn't. Every lookup the REPL can do is also a command with structured output,
and there's an MCP server and a skill for coding agents, so it's "CIDER for agents" as well. Five releases in three weeks:

- [neorepl 0.1.0](https://github.com/nrepl/neorepl/releases/tag/v0.1.0) had the REPL with a proper line editor (highlighting, completion, arglists as you type, history per project, a Ctrl-C that actually interrupts), CIDER-style REPL commands, jack-in for Leiningen, the Clojure CLI, Babashka and shadow-cljs, and commands for evaluating code and looking things up that keep their session between calls. It runs on the JVM and on Babashka.
- [neorepl 0.2.0](https://github.com/nrepl/neorepl/releases/tag/v0.2.0) added a test runner (Clojure and ClojureScript), a full-screen inspector and a stacktrace browser.
- [neorepl 0.3.0](https://github.com/nrepl/neorepl/releases/tag/v0.3.0) was all about coding agents: `neorepl mcp`, a skill that teaches agents to check, load, test and debug Clojure code with the CLI, and `neorepl check --fix`, which fixes mismatched delimiters the way parinfer does.
- [neorepl 0.4.0](https://github.com/nrepl/neorepl/releases/tag/v0.4.0) brought tracing, switching the REPL into another named session (e.g. to take a look at what your coding agent is doing), and a huge list of fixes. I wrote about it (and the two releases before it) in [neorepl 0.4: Tests, Inspector, Tracing and Agents]({% post_url 2026-10-30-neorepl-0-4 %}).
- [neorepl 0.4.1](https://github.com/nrepl/neorepl/releases/tag/v0.4.1) caught up with cider-nrepl 0.64.

proof checks every server with the exact requests neorepl sends, and neorepl went through `proof proxy` and every scenario of `proof serve` before 0.1 was out.
Its commands are separate processes sharing a session, so neorepl hits nREPL's interrupt bugs harder than most clients,
and making it work as well on Babashka's server as with cider-nrepl is where most of the Babashka reports came from.
Even JLine got a few bug reports along the way.

By the time I got around to writing this update, [neorepl 0.5](https://github.com/nrepl/neorepl/releases/tag/v0.5.0) was out as well,
with a much smarter line editor, a namespace browser, automatic reloading and a CLI that editors can use without a plugin.
I wrote about it in [neorepl 0.5: CIDER in a Terminal]({% post_url 2026-11-05-neorepl-0-5 %}).

## nREPL

[nREPL 1.8.0](https://github.com/nrepl/nrepl/releases/tag/v1.8.0) came out on October 2 with the URL-based connections and the TLS improvements I mentioned last time,
plus a few session fixes. Sessions survive evaluated code closing `*in*` or `*out*`, a race that could split responses between concurrent reads in `nrepl.core/client` is gone,
and Sashko made persistent sessions re-create their thread when a faulty exec thunk kills it. He also moved the Unix sockets to the `SocketChannel` API.

Master moved on quickly after the release, much of it because of proof and neorepl:

- the `done` of an eval interrupted from another connection goes to the eval's own connection, where the client that sent the eval never got it
- the server sets `TCP_NODELAY` on accepted connections, so on Linux an eval's `done` no longer waits up to 40ms behind its value
- Sashko replaced `:out-limit` with separate `:out-quota` and `:err-quota` limits, made the bencode transport turn empty lists into `nil` for `?`-ending keys and made building the middleware stack faster

The interrupt bugs that are still open are next on my list.

## Piggieback

- [Piggieback 0.7.1](https://github.com/nrepl/piggieback/releases/tag/0.7.1) fixed `load-file` failing with "No such namespace" when the file requires a foreign lib (e.g. a cljsjs package).
- [Piggieback 0.8.0](https://github.com/nrepl/piggieback/releases/tag/0.8.0) fixed `:cljs/quit` on nREPL 1.3+, where the session stayed in ClojureScript mode, and made evaluation use the same dynamic bindings as `cljs.repl`. That means the `cljs-repl` options (e.g. `:warnings`) and the bindings tools like figwheel-main set up around it finally take effect, which closes an issue from 2018. It also picked up `:as-alias` keywords and the `:cljs` branch of `data_readers.cljc`.

## nrepl.org and cider.mx

nrepl.org got a facelift at the end of October. The manual moved to [docs.nrepl.org](https://docs.nrepl.org),
and [nrepl.org](https://nrepl.org) itself is now a landing page that explains what nREPL is and how its protocol works,
with real exchanges between a client and a server next to every feature. It even has a ["Try it"](https://nrepl.org/#try) section with an nREPL server
running right in your browser, which turned out to be [a story of its own]({% post_url 2026-11-01-nrepl-right-in-your-browser %}).
That playground is how [nrepl-sci](https://github.com/nrepl/nrepl-sci), a small nREPL server for SCI that runs entirely in a web page, came to be,
and it even led me to take a closer look at [clojure-mode](https://github.com/nextjournal/clojure-mode) for CodeMirror, which powers the playground's editor.

CIDER got a landing page of its own at [cider.mx](https://cider.mx), made to look like an Emacs frame (mode line, `M-x` and all), with mini screencasts of CIDER in action.
I had a lot of fun working on both sites!

## neat, mezcaml and nREPL on the BEAM

My toy nREPL implementations were the first things I ran proof against, and all of them got a round of fixes in early October:

- dialtone and repartee, the Erlang and Elixir servers in [nrepl-beam](https://github.com/nrepl/nrepl-beam), now answer `close` with `session-closed`, echo the op in `unknown-op` replies and return a value for every Erlang form in an eval
- [mezcaml](https://github.com/nrepl/mezcaml) no longer lets late output from one eval end the next one early, interrupts on Ctrl-C, answers stdin requests, closes its session on exit and completes on jank and Basilisp
- [neat](https://github.com/nrepl/neat) shows late and id-less output, reports error statuses that come without `err`, handles unknown sessions and stdin EOF, and checks `describe` before using completions, lookup and interrupt

neat's own integration suite now tests just the client and leaves the servers to proof. None of the three has had a release yet, though.

## CIDER

No CIDER release this time (2.1 is still brewing), but master kept moving:

- Test results now stream in while the tests run (`cider-test-stream-results`, on by default): the echo area keeps a running count, and failing tests land in the report as soon as they finish. This closes an issue from 2014! `cider-test-fail-fast` is now off by default to go with it.
- A family of commands for editing the `ns` form: `cider-ns-add-require`, `cider-ns-add-import`, `cider-ns-add-missing` (adds what the symbol at point needs), magic requires (typing `alias/` adds the require the alias needs) and `cider-ns-clean`. These are some of clj-refactor's most popular features, and they're backed by new cider-nrepl ops that read your code instead of loading it.
- Connecting to a server without cider-nrepl (e.g. plain nREPL or Babashka) finishes setting up the connection again, where `cider-connected-hook` and friends never ran. proof found this one.
- Keys with a `nil` value are left out of nREPL requests instead of being sent as empty lists, which closes an issue from 2020.
- Natively compiled builds no longer have broken menu options, Emacs 31 is on CI, and the manual got per-build-tool setup tabs, the refcard and CI checks for its cross references.

## cider-nrepl and Orchard

[cider-nrepl](https://github.com/clojure-emacs/cider-nrepl) releases:

- [cider-nrepl 0.63.0](https://github.com/clojure-emacs/cider-nrepl/releases/tag/v0.63.0) runs tests with `orchard.test` and streams their progress to the client, which is what CIDER's test streaming builds on.
- [cider-nrepl 0.63.1](https://github.com/clojure-emacs/cider-nrepl/releases/tag/v0.63.1) was Sashko's quick bump to compliment 0.8.2.
- [cider-nrepl 0.64.0](https://github.com/clojure-emacs/cider-nrepl/releases/tag/v0.64.0) added the `cider/resolve-missing`, `cider/resolve-alias` and `cider/clean-ns` ops behind CIDER's `ns` commands (after three alphas), made the test ops always answer and send what tests print with their own responses on nREPL 1.3+, and fixed the inspector and ClojureScript tests on shadow-cljs.

[Orchard](https://github.com/clojure-emacs/orchard) releases:

- [Orchard 0.45.0](https://github.com/clojure-emacs/orchard/releases/tag/v0.45.0) added `orchard.test`, the `clojure.test` runner that used to live in cider-nrepl and can now report progress as it goes, plus Sashko's string analytics in the inspector and his fix for the stack overflows when printing recursive collections.
- [Orchard 0.46.0](https://github.com/clojure-emacs/orchard/releases/tag/v0.46.0) and [Orchard 0.47.0](https://github.com/clojure-emacs/orchard/releases/tag/v0.47.0) added `orchard.ns-form`, which takes apart an `ns` form and tells what it requires, refers and imports that the code doesn't use, all without loading anything, and fixed some Java doc rendering problems.
- [Orchard 0.48.0](https://github.com/clojure-emacs/orchard/releases/tag/v0.48.0) made tracing show calls that throw and stopped tracing a function tracing itself uses (say, `str`) from overflowing the stack.

Sashko also added JDK 27 to the CI of nREPL, cider-nrepl and Orchard. Thanks, Sashko!

## refactor-nrepl and clj-refactor

[refactor-nrepl 3.15.0](https://github.com/clojure-emacs/refactor-nrepl/releases/tag/v3.15.0) documented the limitations of its AST-based analysis
and deprecated the ops cider-nrepl has taken over. Back in the spring I said I'd like to move the most useful refactor-nrepl features
to CIDER and cider-nrepl eventually, and the `ns` editing commands above are the first step in that direction.

## clojure-ts-mode

No release, but quite a few fixes on main. I fixed clojure-ts-mode on Emacs 31 (e.g. the ClojureScript mode asking for the jsdoc grammar, `M-q` indenting only the innermost form)
and a pile of refactoring commands that went wrong around metadata, `#_` forms and reader conditionals.
Jason Jackson sent a round of indentation and alignment fixes (threading macros, reader prefixes, metadata and comments before arguments),
and Kyle Cesare kept up with jank's syntax. Thanks, Jason and Kyle!

## MrAnderson and smaller things

[MrAnderson 0.7.2](https://github.com/benedekfazekas/mranderson/releases/tag/v0.7.2) stopped bundling pomegranate's Maven classes in its jar,
where they clashed with any other resolver on the classpath and broke cider-nrepl's build once tools.build pulled in a newer tools.deps. The artifact went from 8 MB to about 200 KB in the process.

There was also a lot of release and CI maintenance across nREPL, cider-nrepl, Orchard, Piggieback and friends
(e.g. release notes taken from the changelogs, the docs of a release published by its release workflow), and I revived the CI of parseclj and parseedn.

## Blog posts

- [neorepl: A Terminal REPL for Clojure (and Friends)]({% post_url 2026-10-10-neorepl-a-terminal-repl-for-clojure-and-friends %})
- [neorepl 0.4: Tests, Inspector, Tracing and Agents]({% post_url 2026-10-30-neorepl-0-4 %})
- [nREPL, Right in Your Browser]({% post_url 2026-11-01-nrepl-right-in-your-browser %})
- [neorepl 0.5: CIDER in a Terminal]({% post_url 2026-11-05-neorepl-0-5 %})
- [proof: How Strong Is Your nREPL?]({% post_url 2026-11-05-proof-how-strong-is-your-nrepl %})

## Epilogue

Big thanks to Clojurists Together, Nubank and the other organizations and people supporting my Clojure OSS work! None of this would have happened without you. You rock!

As for what's next - last time I said CIDER 2.1 was the obvious next milestone. It still is, but it got delayed, as most of my time went into nREPL instead.
I told you I'm bad at predictions! nREPL 1.8 shipped as planned, and mezcaml and the BEAM servers got a lot more reliable thanks to proof, even if neither got a release.
While we've made solid progress on CIDER and friends as well, my main focus until the end of the year will likely be the broader nREPL ecosystem:
fixing the interrupt bugs proof found in nREPL, turning the list of spec changes into PRs against the spec, and getting more servers and clients to run proof.

Keep hacking!
