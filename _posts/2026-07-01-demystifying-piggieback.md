---
layout: post
title: "nREPL and ClojureScript: Demystifying Piggieback"
date: 2026-07-01 12:00 +0300
categories: posts
tags:
- Clojure
- ClojureScript
- nREPL
- CIDER
---

> I can't carry it for you... but I can carry you!
>
> -- Samwise Gamgee, on the virtues of piggyback

If you've ever fired up a [ClojureScript](https://clojurescript.org) REPL from your editor and it *just
worked*, there's a decent chance [Piggieback](https://github.com/nrepl/piggieback)
was quietly doing the heavy lifting behind the scenes. It's one of those
libraries that's been around forever, that everyone in the [CIDER](https://www.cider.mx) world depends
on, and that almost nobody actually understands. For years I counted myself
firmly in the "almost nobody" camp.

That changed recently. I finally had to do some serious work on Piggieback
myself, and to my mild horror I realized I'd forgotten most of how it works
internally. So I did what I always do when I need to understand something
properly - I dug in, refactored it into a shape I could actually reason about,
and then wrote it all down. This article is that write-up.

<!--more-->

## A little backstory

Piggieback was created by [Chas Emerick](https://github.com/cemerick) back in
August 2012. The very first commit is dated August 10th, 2012, which makes the
project a few weeks shy of *fourteen years old* as I write this. That's a very
long time in software years.

The name is a pun, of course - Piggieback rides *piggyback* on top of an existing
[nREPL](https://nrepl.org) server. Why "piggie" and not "piggy"? That's one of life's great unsolved
mysteries. Only Chas knows, and he's not telling.[^name]

The problem it set out to solve was very concrete. nREPL talks to a Clojure
runtime. ClojureScript, meanwhile, compiles to JavaScript and runs somewhere
*else* entirely - a browser tab, a Node process, back in the day even
[Rhino](https://en.wikipedia.org/wiki/Rhino_(JavaScript_engine)) or [Nashorn](https://en.wikipedia.org/wiki/Nashorn_(JavaScript_engine))
running inside the same JVM. So how do you get your editor, which only knows how
to talk nREPL to a JVM, to evaluate ClojureScript in some far-flung JavaScript
runtime? Piggieback's answer was pretty clever: don't build a new protocol,
don't build a new server, just *hijack* the existing nREPL session and quietly
reroute its evaluation to ClojureScript.

The project changed hands a couple of times over the years. It started life as
`com.cemerick/piggieback`, deployed to Maven Central. When Chas stepped back it
moved under the CIDER umbrella and became `cider/piggieback` on Clojars, and
eventually landed in the [nREPL org](https://github.com/nrepl) where it lives
today. We kept the `cider` group id to avoid breaking everyone's `deps.edn` for
no good reason.[^groupid]

Historically I wasn't the one driving Piggieback's development. That credit goes
to a few other people - Chas himself, of course, but also
[Bruce Hauman](https://github.com/bhauman) (of [Figwheel](https://figwheel.org) fame, who shaped a lot of
the evaluation machinery) and [Michael Griffiths](https://github.com/cichli) (who
did a ton of work on printing and nREPL integration). For a long time I was happy
to let Piggieback tick along in the background while smarter people than me
handled its guts.

But times change. These days CIDER and the rest of its supporting projects,
Piggieback very much included, have exactly two active maintainers: me and
[Alex Yakushev](https://github.com/alexander-yakushev). When you're down to a crew
that small, "I'll let someone else understand the ClojureScript internals" stops
being a viable strategy. So I sat down and learned. Eventually we all have to, I
guess. :D

Which brings me to a small confession: neither Alex nor I is an actual
ClojureScript expert. We're Clojure folks who wandered into the cljs internals out
of necessity, and we muddle through. So if you *are* one, and you've got a bit of
time and goodwill to spare, help with the development of Piggieback (and CIDER
more broadly) would be most appreciated. There's plenty of interesting work to go
around!

## Two languages, one server

Here's the thing that still makes me smile about Piggieback's design: a single
nREPL server can host *both* Clojure and ClojureScript evaluation at the same
time, on a per-session basis. One editor connection can be running Clojure,
another can be running ClojureScript, and the server doesn't blink.

And the *client* doesn't have to do anything special either. There's no
`:env :cljs` parameter to pass, no dedicated `cljs-eval` op to call. You keep
sending plain old `eval` messages with your session id, exactly as you would for
Clojure. Piggieback figures out the rest.

The way you "enter" ClojureScript is by evaluating a single form in your session:

```clojure
(require 'cljs.repl.node)
(cider.piggieback/cljs-repl (cljs.repl.node/repl-env))
;; To quit, type: :cljs/quit
```

From that point on, every `eval` and `load-file` in *that session* is
ClojureScript, until you send `:cljs/quit` to switch back. Meanwhile your
colleague's session on the same server is still merrily evaluating Clojure. It's
a bit like flipping a switch that only affects your own room in a shared house.

This is what I mean by session-based dispatch, and it's the conceptual heart of
the whole thing:

```
 nREPL server (one JVM)
 +-----------------------------------------------------------+
 |                                                           |
 |  session A --eval-->  Clojure                             |
 |                                                           |
 |  session B --eval-->  ClojureScript  -->  Node runtime    |
 |                                                           |
 |  session C --eval-->  ClojureScript  -->  browser tab     |
 |                                                           |
 +-----------------------------------------------------------+
```

## How it actually works

> These aren't the droids you're looking for.
>
> -- Obi-Wan Kenobi, nREPL middleware

Let's peel back a layer. Piggieback is, first and foremost, an nREPL
*middleware* - a function that wraps the nREPL handler and gets a crack at every
message before (or instead of) the default machinery.

Its middleware, `wrap-cljs-repl`, does something pretty simple: for each incoming
message, it checks whether the session currently has an active ClojureScript
REPL. If it does *and* the op is `eval` or `load-file`, Piggieback handles the
message itself. Otherwise it passes the message straight through to the normal
Clojure handling. Distilled to its essence, it looks like this:

```clojure
(defn wrap-cljs-repl [handler]
  (fn [{:keys [session op] :as msg}]
    (if (and (@session #'*cljs-repl-env*)          ; are we in cljs mode?
             (#{"eval" "load-file"} op))           ; and is this an eval?
      (piggieback-eval msg)                        ; yes: hijack it
      (handler msg))))                             ; no: business as usual
```

The `*cljs-repl-env*` dynamic var stored in the session is the linchpin. Its
presence *is* the signal "this session is doing ClojureScript". It's also what
other middleware, like [cider-nrepl](https://github.com/clojure-emacs/cider-nrepl), look for to know they're dealing with a cljs
session - more on that in a bit.

Here's the bigger picture, showing where Piggieback sits in the stack:

```
   editor                         JVM: nREPL server
 +---------+   nREPL protocol   +-------------------------------------------+
 | CIDER,  |<--(bencode/edn)--->|  transport + session                      |
 | Calva,  |                    |        |                                  |
 | vim,    |                    |        v   middleware stack               |
 | ...     |                    |   wrap-print > wrap-cljs-repl > eval ...   |
 +---------+                    |                    |                      |
                                |      (eval/load-file, only when the       |
                                |       session has *cljs-repl-env* set)    |
                                |                    v                      |
                                |      cljs.repl  (IJavaScriptEnv)          |
                                |                    |                      |
                                +--------------------+----------------------+
                                                     v
                                       Node process  /  browser tab  /  ...
```

The clever bit is that Piggieback is written in *Clojure* and runs on the JVM,
yet it drives evaluation in ClojureScript. It does this through ClojureScript's
own Clojure-facing API - specifically the `cljs.repl/IJavaScriptEnv` protocol,
which every ClojureScript REPL environment implements:

```clojure
(defprotocol IJavaScriptEnv
  (-setup    [repl-env opts]         "initialize the environment")
  (-evaluate [repl-env filename line js] "evaluate a javascript string")
  (-load     [repl-env provides url] "load code at url into the environment")
  (-tear-down [repl-env]             "dispose of the environment"))
```

So the flow, roughly, is this. Your ClojureScript form comes in as text.
Piggieback reads it with the ClojureScript reader, the ClojureScript compiler
turns it into JavaScript, and `-evaluate` ships that JavaScript off to the
runtime (Node, browser, whatever). The result comes back as a string, Piggieback
reads it and sends it to your editor as an ordinary nREPL `:value`. Your editor
has no idea a whole JavaScript runtime was involved.

### Starting the REPL vs. evaluating in it

There are actually two distinct code paths inside Piggieback, and understanding
the split explains a lot of its quirks.

*Setup* is the fiddly one. To bootstrap a ClojureScript REPL you need to load
`cljs.core`, set up the analyzer, wire up the compiler environment, and require a
bunch of REPL helpers into `cljs.user`. Rather than reimplement all that,
Piggieback leans on ClojureScript's own `cljs.repl/repl*` - it briefly drives the
real REPL loop, feeds it a single namespace-require form, and then bails out.

There's a funny wrinkle here. `cljs.repl/repl*` insists on tearing the
environment down when its loop exits, which would immediately kill the Node
process we just launched. Piggieback's workaround is to wrap your repl-env in a
*delegating* environment whose `-tear-down` is a deliberate no-op, forwarding
every other method to the real thing:

```clojure
(deftype DelegatingReplEnv [repl-env]
  cljs.repl/IJavaScriptEnv
  (-setup    [_ opts]           (cljs.repl/-setup repl-env opts))
  (-evaluate [_ filename line js] (cljs.repl/-evaluate repl-env filename line js))
  (-load     [_ provides url]   (cljs.repl/-load repl-env provides url))
  (-tear-down [_])              ; <- the whole point: swallow the tear-down
  ;; ... plus map-like access, delegated to repl-env ...
  )
```

*Steady-state evaluation* is the other path, and it's the one your editor hits on
every eval. Here Piggieback skips the REPL loop entirely and calls
`cljs.repl/evaluate-form` directly, reusing the compiler environment and repl-env
it squirreled away in the session during setup. One form in, one printed value
out.

```
 (cljs-repl node-env)          (eval "(+ 1 1)")
        |                              |
        v                              v
   drive repl* once             read cljs form
   (delegating env)                   |
        |                       compile to JS
   -setup: launch Node                |
        |                        -evaluate  -->  Node  -->  "2"
   stash repl-env +                   |
   compiler-env in session      read back, send :value
```

### The compiler environment, and why your tools love it

Now for my favourite part, because it's the bit that makes ClojureScript tooling
actually good rather than merely functional.

When Piggieback sets up a REPL, it stashes the ClojureScript *compiler
environment* in the session, under a well-known var:

```clojure
;; in the session atom, keyed by the var itself:
{#'cider.piggieback/*cljs-compiler-env*  <the compiler env atom>
 #'cider.piggieback/*cljs-repl-env*      <the (delegating) repl-env>}
```

That compiler environment is a treasure trove. It holds the analyzer's full
picture of your program - every namespace, every var, their arglists,
docstrings, source locations, the works. And because Piggieback parks it
somewhere stable and predictable, *other* middleware can reach in and grab it.

That's exactly what cider-nrepl does. When you ask CIDER for completion
candidates, or arglists, or "jump to definition" in a ClojureScript buffer,
cider-nrepl pulls the compiler env out of the session:

```clojure
;; cider-nrepl, roughly:
(get @session #'cider.piggieback/*cljs-compiler-env*)
```

...and then runs static analysis against it. No extra round-trip to the browser,
no runtime reflection, just the compiler's own knowledge sitting right there for
the taking. It's a nice example of how a small, stable contract between two
libraries enables a whole class of features neither could build alone. Every time
autocompletion works in a `.cljs` buffer, this little handshake is why.

## Which environments does it support?

Because Piggieback talks to the generic `cljs.repl/IJavaScriptEnv` protocol, it
works with pretty much *any* standard ClojureScript REPL environment. These days
that mostly means Node.js (`cljs.repl.node`) - the workhorse for non-browser
development, scripts, and, importantly, test suites; this is what Piggieback's own
tests run against. The other big one is the browser environment
(`cljs.repl.browser`), which evaluates in a real browser tab via a little
websocket dance and is great for actual web development. Beyond those there's
[GraalJS](https://www.graalvm.org/latest/reference-manual/js/) and anything else that happens to ship an `IJavaScriptEnv`
implementation.

A couple of historical footnotes are worth a mention too. The very first versions
of Piggieback supported Rhino (dropped in 0.3.0) and later Nashorn, both in-JVM
JavaScript engines that have since faded from relevance. The tests were moved off
Nashorn and onto Node years ago.

There's one category Piggieback can't help with, though: self-hosted
ClojureScript, like [Lumo](https://github.com/anmonteiro/lumo) or [Planck](https://planck-repl.org).[^selfhosted] Piggieback is fundamentally a Clojure program
leaning on ClojureScript's *Clojure* API. If there's no JVM in the picture at
all, there's nothing for it to hook into. For those you want a native
ClojureScript nREPL implementation like
[nrepl-cljs](https://github.com/djblue/nrepl-cljs) instead.

## The limitations

Piggieback's simplicity is its greatest strength, but simplicity always has a
price. Let me be honest about the sharp edges.

The most annoying one is that you can only run *one* Node REPL per JVM.
ClojureScript's `cljs.repl.node` keys some of its internal state on the *thread
name*, which collapses nREPL's worker threads together, so two Node REPLs in the
same JVM end up clobbering each other. This one's upstream, not Piggieback's
fault, but it's a real ceiling.

Then there's the fact that you can't interrupt a running evaluation. A
ClojureScript form runs in the JavaScript runtime, on a thread Piggieback doesn't
control, and there's no portable way to interrupt code that's already executing
over there. So a runaway expression means restarting the runtime - Clojure's
`interrupt` op simply doesn't apply here.

Multi-form evaluation is limited too. For performance reasons Piggieback doesn't
spin up a fresh REPL for each evaluation, which means that if you send several
top-level forms in a single message, only the first one gets evaluated. Rarely an
issue in practice, but a sharp edge nonetheless.

And finally, Piggieback is coupled to ClojureScript's compiler internals. There's
no public "evaluate this cljs form and hand me the result" API, so it has to reach
into `cljs.analyzer`, `cljs.env`, and the non-public bits of `cljs.repl`. It
works because ClojureScript changes slowly, but it's inherently a bit fragile.

None of these are dealbreakers for the things Piggieback is good at. But they do
point at why it's not the only game in town.

## The other approach: shadow-cljs

If you've done any serious ClojureScript app development in the last several
years, you've almost certainly used
[shadow-cljs](https://github.com/thheller/shadow-cljs), Thomas Heller's build
tool. And here's a fact that surprises a lot of people: shadow-cljs does *not* use
Piggieback. It ships its own nREPL middleware.

The philosophical difference is worth spelling out. Piggieback is a thin *bridge*
to the official `cljs.repl` environments; it assumes very little and owns very
little. shadow-cljs takes the opposite stance - it owns the *entire* build and
runtime lifecycle (watching your files, hot-reloading code, managing multiple
runtimes, tracking which build is which), and its nREPL integration is a natural
extension of all that machinery. When you eval a form in a shadow-cljs setup it
routes to whichever runtime is attached to your build, with hot-reload and the
rest already in play.

So which is "better"? That's the wrong question, in my opinion. They're solving
overlapping but quite different problems, and the right choice depends almost
entirely on *what kind of ClojureScript environment you're working in*. If you're
building a web app with a modern toolchain, shadow-cljs's integrated approach is
probably a better fit and a nicer experience overall - the hot-reload story alone
is worth a lot. If you're on a plain deps.edn or [Leiningen](https://leiningen.org) project, poking at a
Node REPL, or building tooling that targets the *official* ClojureScript REPL,
Piggieback's minimalism is exactly what you want. The flip side of shadow-cljs
owning everything is that you have to buy into its world; the flip side of
Piggieback owning almost nothing is that ceiling of one Node REPL per JVM. Both
are entirely valid trade-offs. I use both, depending on the day.

## What we've been up to lately

Piggieback had drifted into maintenance mode for a while, so a big chunk of my
recent work was simply *modernizing* it - part of the same recent burst of energy
that's producing [CIDER 2.0]({% post_url 2026-06-30-cider-2-0-is-brewing %}) - and
then fixing a pile of long-standing bugs now that I actually understood the thing.

On the correctness front, the recent 0.6.x and 0.7.0 releases sorted out a batch
of old issues: ClojureScript output going missing after reconnecting to a
session ([#111](https://github.com/nrepl/piggieback/issues/111)), evaluation
breaking after a REPL setup error
([#62](https://github.com/nrepl/piggieback/issues/62)), garbled printing of
unknown tagged literals
([#120](https://github.com/nrepl/piggieback/issues/120)), and a cryptic error
when `cljs-repl` was invoked outside a session
([#124](https://github.com/nrepl/piggieback/issues/124)). On top of that,
`load-file` now evaluates the source that's actually in your editor buffer,
unsaved changes and all, instead of silently re-reading the file from disk - which
is how nREPL has always behaved for Clojure. Closing a session now tears down its
ClojureScript REPL too, so you stop leaking Node processes when you disconnect
without a polite `:cljs/quit`. And `describe` finally advertises ClojureScript
status, so tools can detect that a session is in cljs mode straight from the
protocol instead of having to guess.

There was also a serious bit of internal restructuring. The old three-file
`load`/`in-ns` contraption is gone, all the ClojureScript coupling now lives
behind one clean namespace, and the runtime code generation for the delegating env
was replaced with a single, comprehensible type. The public contract didn't change
one bit; the insides are just far less scary now. If you want the gory details, I
wrote both an
[architecture document](https://github.com/nrepl/piggieback/blob/master/docs/architecture.md)
and a [roadmap](https://github.com/nrepl/piggieback/blob/master/docs/roadmap.md)
while I was in there - partly for future contributors, partly for future me, who
will inevitably forget all of this again.

## What's next?

A few things I've been ruminating on for the future. Piggieback still drives
`cljs.repl/repl*` for the initial bootstrap, which is the source of most of its
remaining awkwardness. In principle we could replicate just the setup we need and
unify the two eval paths into one, but that trades one kind of coupling for
another, so I've deferred it until the benefit clearly outweighs the risk.

There are also a couple of things I'd love to see fixed *upstream* in
ClojureScript itself, because they're not really Piggieback's problems: the
thread-name state in `cljs.repl.node` that caps us at one Node REPL per JVM, and
the way the Node env captures `*out*` at setup time. Fixing those upstream would
help everyone, shadow-cljs included. And if those pieces land, better
multi-runtime support becomes a real possibility. None of it is urgent, though.
Piggieback works, and works well, for what it does.

## Epilogue

If there's one thing I hope you take away from this, it's that Piggieback is *not*
as scary as its reputation suggests. Underneath the intimidating "it evaluates
ClojureScript over nREPL" description is a genuinely simple idea - hijack a
session, delegate to `cljs.repl`, stash the compiler env where tools can find it -
with a bit of cleverness sprinkled on top. It's a great example of how a small,
sharp design can endure for well over a decade with its core intact.

For years the community kept expecting native nREPL servers running on
self-hosted ClojureScript to show up and make Piggieback obsolete. Those never
really materialized. Piggieback, meanwhile, is still here, still doing its job,
still quietly powering ClojureScript REPLs in editors everywhere. It's clearly not
the only game in town anymore - shadow-cljs's approach is a better fit for a lot
of people, and that's great. But there's something to be said for a little library
that picked a simple design back in 2012 and rode it all the way to today.

That's from me, folks! Keep hacking!

[^name]: The official project FAQ's answer to "Why 'piggieback' instead of 'piggyback'?" is, and I quote, "That's one of life's greatest mysteries. Only Chas can answer that one." I've decided to preserve the mystery.
[^groupid]: This is also why the main namespace is `cider.piggieback` rather than `nrepl.piggieback`. Renaming it would break every existing config for zero real benefit. Boring stability beats exciting churn.
[^selfhosted]: The self-hosted ClojureScript scene has gone rather quiet, by the way. Lumo was [archived back in 2022](https://github.com/anmonteiro/lumo) and Planck hasn't cut a release [since 2024](https://github.com/planck-repl/planck/releases). These days the "Clojure without a JVM" itch mostly gets scratched by SCI-based tools like [nbb](https://github.com/babashka/nbb) and [scittle](https://github.com/babashka/scittle) - a different beast (an interpreter, not the self-hosted compiler), but very much alive. Which makes that native ClojureScript nREPL server I keep pining for feel further away than ever.
