---
layout: post
title: "neorepl: A Terminal REPL for Clojure (and Friends)"
date: 2026-10-10 12:38 +0300
tags:
- Clojure
- nREPL
- neorepl
---

For more than a decade the Clojure REPL most people saw in a terminal was
[REPLy](https://github.com/trptcolin/reply), the one behind `lein repl`. It
served us well, but it's showing its age, and I kept wishing for a terminal
REPL that felt like CIDER, without the Emacs part. So I finally built one.
[neorepl](https://github.com/nrepl/neorepl) 0.1 is out today!

<!--more-->

## Why another REPL?

There's no shortage of Clojure REPLs, so it's fair to ask. Here's what I was
after:

- nREPL all the way down. neorepl only speaks nREPL, so it works with anything
  that runs an nREPL server: Clojure, babashka, shadow-cljs, Basilisp,
  ClojureCLR and so on. The smarts live on the server. When cider-nrepl is
  there neorepl uses it, the same way CIDER does, and when it isn't it falls
  back to nREPL's built-in ops, and then to evaluating a bit of code.
- A REPL that's pleasant to use: a proper line editor, highlighting,
  completion, arglists as you type, pretty printing, history per project and
  a Ctrl-C that actually interrupts the evaluation.
- A CLI that's just as good for scripts and coding agents. Every lookup the
  REPL can do is also a command, with structured output and exit codes that
  mean something.
- Something small and modern. Clojure 1.10+, nREPL and JLine 4 are the only
  dependencies, and the same code runs on the JVM and on babashka. The whole
  thing is about 3,300 lines right now, and I'd like to keep it that way.

`rebel-readline` and `bb repl --connect` are both great, and I borrowed from
them freely, but neither tries to be an nREPL-first tool with CIDER's tooling,
jack-in and a CLI for agents. That combination is the whole point of neorepl.

**Update (2026-10-11):** A few people asked how neorepl relates to the
[Clojure CLI REPL](https://github.com/clojure/clojure-cli.repl), which the
Clojure team released a couple of weeks ago, and to `bb repl --connect`. (I'm a
bit embarrassed to admit I hadn't heard of the former until after I announced
neorepl.) Funny enough, all three are nREPL clients under the hood, but they're
after pretty different things. In practical terms:

- The Clojure CLI REPL runs only on the JVM and needs Clojure 1.12+. It ships
  its own nREPL server, and its docs and data inspector are ops of that server,
  so against any other server you can only evaluate code. It's deps.edn only.
  Its editing is really nice, though - structural editing, evaluating the form
  at the cursor and a `datafy`-based inspector - and I'll be borrowing some
  ideas from it. It doesn't do completion yet.
- `bb repl --connect` puts babashka's console REPL in front of any nREPL server,
  with completion, arglists and docs, in very little code. It's a great way to
  poke at a running server, but it won't start one for you.
- neorepl works with any nREPL server and gets the most out of the ones with
  cider-nrepl (or babashka's take on it), without any middleware of its own.
  It starts a server for Leiningen, deps.edn, babashka and shadow-cljs projects,
  follows you into ClojureScript, and everything the REPL does is also a
  command with JSON output for scripts and agents. No structural editing yet,
  however.

So, if you want the fanciest line editing and you live in deps.edn land, give
the Clojure CLI REPL a try. If you want CIDER in your terminal, for you and
for your agents, that's what neorepl is for. The neat part is that babashka's
server bundles CIDER's inspector and test runner ops these days, so neorepl's
test runner (already on `main`) and the inspector that's coming next work with
babashka out of the box.

## For humans

Here's the REPL in action:

![The neorepl REPL](/assets/images/neorepl-repl.gif)

A few things worth pointing out:

- Enter only submits the input once its forms are complete, so pasting a whole
  function just works.
- TAB completes, with the candidates' types next to them, and the arglist of
  the function you're calling shows up after the cursor.
- Values get pretty printed on the server and highlighted on the client.
- Commands start with a comma, the CIDER way: `,doc`, `,source`, `,apropos`,
  `,macroexpand`, `,pst`, `,reload`, `,edit` (which opens a definition in your
  `$EDITOR`) and `,help` for the rest. The Clojure reader sees a comma as
  whitespace, so they can't clash with real code.
- Ctrl-C interrupts what's running, and code that reads `*in*` gets its input,
  something REPLy never quite got right.

Without a server to connect to, `neorepl` starts one for the project, the way
CIDER's jack-in does, with cider-nrepl on the classpath, and stops it when you
exit. It knows Leiningen, the Clojure CLI, babashka and shadow-cljs projects.

ClojureScript works too. Start a ClojureScript REPL in the session (through
shadow-cljs or piggieback, which jack-in adds for you) and neorepl follows
along: evaluation, completion, docs and the rest switch to ClojureScript, and
`:cljs/quit` takes you back.

## For agents

Coding agents are surprisingly good at Clojure, as long as they have a REPL
to talk to. Most of them drive CLIs much better than anything else, so neorepl
has a CLI that's meant to be driven:

![neorepl's CLI](/assets/images/neorepl-agent.gif)

- `neorepl eval` evaluates code on the project's server. Calls share a
  session, so `*ns*`, `*1` and `*e` carry over from one call to the next, and
  `--session` keeps separate lines of work apart.
- `--format json` (or `edn`) gives you the values, the output and the
  namespace as data.
- `--timeout` interrupts the evaluation before giving up, so an infinite loop
  doesn't take the server down with it, and `--max-output` keeps a stray
  `(range)` from eating the agent's context.
- `neorepl check` finds unbalanced parens, brackets and braces without a
  server. That's the most common way for an agent (or a human) to break a
  Clojure file.
- `neorepl doc`, `source`, `apropos`, `complete`, `where`, `macroexpand` and
  `reload` do what the REPL's commands do.
- `neorepl server` starts a server that stays up, for editors, agents and
  `neorepl eval` to share.

The exit codes are boring on purpose: 0 when all went well, 1 for an
evaluation error, 3 when there's no server to talk to and 124 for a timeout
(like `timeout(1)`).

## Playing nicely with nREPL

A new client is also a new way to break servers, or to be broken by them, and
the nREPL world has quite a few servers these days. Fortunately I've been
working on [proof](https://github.com/nrepl/proof), a compatibility suite for
nREPL, which made neorepl a good test subject.

proof checks both sides of the conversation. `proof proxy` sits between a
client and a server and grades everything the client sends: every request has
an id, `need-input` gets answered with `stdin` in the right session, and so on.
`proof serve` is a small nREPL server that can behave like other servers in
very specific ways - sending only the last value of the code like Basilisp and
jank, dropping stderr, having no `interrupt` op, splitting output into one
message per character, and so on. I ran neorepl's REPL and CLI through both,
against nREPL, babashka, Basilisp and jank, and through every quirk `proof
serve` knows about.

neorepl came out of it in decent shape, but not unscathed:

- Code read from stdin (`neorepl eval -`) that then read `*in*` itself crashed
  neorepl and left the evaluation waiting on the server for input that was
  never coming.
- neorepl's errors could leave the shell's prompt dangling at the end of a
  line with servers that don't end their error reports with a newline (hello,
  jank!).
- `neorepl eval` exited with 1 when it lost the server, which is the status
  for code that failed.

All of those are fixed in 0.1. It also turned up a couple of things that are
nREPL's fault rather than neorepl's: `need-input` and the end of an
interrupted evaluation can go to the wrong connection when a session is used
from more than one. Those will be fixed in nREPL itself.

It works the other way around too. proof now checks servers with the exact
requests neorepl sends while starting its REPL and evaluating code, next to
the ones CIDER, Calva, Conjure, vim-fireplace and REPLy send, so a server that
breaks neorepl finds out from proof, not from a bug report. nREPL, babashka,
Basilisp and jank all pass those checks today.

## Making the neighbours better

Building neorepl turned up a few problems elsewhere, which is one of my
favourite side effects of writing a new client. piggieback used to evaluate
only the first form you sent it and silently drop the rest, and it loaded the
ClojureScript compiler at startup even when nobody asked for a ClojureScript
REPL, which added almost half a second to the startup of every server with
ClojureScript on the classpath. The fixes for both
([piggieback#162](https://github.com/nrepl/piggieback/pull/162) and
[piggieback#161](https://github.com/nrepl/piggieback/pull/161)) will be in the
next piggieback release. The Node.js
REPL in ClojureScript itself doesn't cope well with interrupted evaluations,
and that one is next on my list.

## What's next

0.1 is the foundation. Here's what I'd like to tackle next:

- a test runner with proper reports
- a full-screen inspector and stacktrace browser
- an MCP server and a ready-made skill for coding agents
- better Windows support

## Try it

The easiest way to install neorepl is [bbin](https://github.com/babashka/bbin):

```shell
$ bbin install io.github.nrepl/neorepl
$ cd my-project
$ neorepl
```

The [README](https://github.com/nrepl/neorepl) has the details.

It's early days, so I'm sure you'll find rough edges. Please file issues,
and tell me what you'd like to see next. And if you're one of the people who
kept `lein repl` and REPLy going over the years - thank you! neorepl wouldn't
exist without you.

Keep hacking!
