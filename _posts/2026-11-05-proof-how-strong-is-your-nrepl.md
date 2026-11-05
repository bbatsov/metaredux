---
layout: post
title: "proof: How Strong Is Your nREPL?"
date: 2026-11-05 23:00 +0200
tags:
- Clojure
- nREPL
- proof
---

For as long as I've been involved with nREPL, the question I've heard most
often from people building servers and clients has been some variation of
"Will this work with CIDER?". And for just as long the honest answer was "Try
it and see". Today I have a better answer for you -
[proof](https://github.com/nrepl/proof), a compatibility suite for nREPL
servers (and clients). I've been working on it since the end of September and
[proof 0.2][release] came out a couple of days ago, so I guess it's high time
I told you a bit about it.

<!--more-->

## Try It and See

The nREPL protocol is tiny. A handful of ops, requests and responses that are
simple maps, bencode on the wire. You can write a basic nREPL server in a
couple of hours, and lots of people have done exactly that. Making it play nice with
CIDER, Calva, Conjure and the rest of the clients out there is a very
different matter, though. What clients actually expect from a server is
spread across the reference implementation, a draft spec, many years of
client code and lots of GitHub issues. Which status values mean what? Is
`ops` in `describe` a map or a list? Where does the end of an interrupted
evaluation go? What happens to a session when its client goes away in the
middle of an eval? Server authors usually find out one bug report at a time.

Client authors don't have it any easier, as every server has its own quirks,
and for many years the only way to find them was to try your client with every
server you could get your hands on. I've been on both sides of this
conversation for well over a decade (CIDER is a client, nREPL is a server), so
believe me - I know the pain.

## Bigger Than Clojure

Why bother? Because I've always seen nREPL as something bigger than Clojure.
Back in 2019 I wrote that nREPL is [similar in nature to
LSP]({% post_url 2019-01-12-nrepl-beyond-clojure %}), only much simpler, and I
still believe that. For any language that can evaluate code at runtime, nREPL
is a much simpler alternative to LSP. Implement `eval`, `interrupt` and
`load-file`, throw in completions and lookups if you feel like it, and every
nREPL client out there becomes an editor integration for your language.

That's not just a dream anymore. These days there are nREPL servers for
Clojure dialects running on Python (Basilisp), C++ (jank), .NET (ClojureCLR),
Go (let-go) and Node.js (nbb), for Janet and Steel Scheme, and even for
Erlang and Elixir (more on those later). Still, "language-agnostic" doesn't
mean much if everyone who builds a server has to check it against CIDER by
hand.

## Better Late Than Never

The idea of a compatibility suite is not new. When nREPL 1.0 came out in 2022
I wrote that [improving the protocol spec and the ability to test against
it](https://github.com/nrepl/nrepl/discussions/273) was where our focus
should go next, and a year later the lack of a compatibility test suite made
my list of [things people hate about
nREPL]({% post_url 2023-03-26-bad-nrepl %}). I even asked if one of you was
looking for a fun small OSS project. Nobody volunteered, and the idea spent
a few more years on the backburner. This year, thanks to the support of
[Clojurists Together](https://www.clojuriststogether.org/), I finally made a
very focused push on it.

It started with the spec. Late last year Phil Hagelberg wrote a draft of [the
nREPL protocol spec](https://spec.nrepl.org), which landed in May. A spec is
great, but you can't run a spec against your server, so I started proof. The
two feed each other - every time proof finds a place where the spec and the
clients disagree, or something the spec doesn't cover at all, it ends up in
[a running list][spec-changes], and from there in the spec itself. (A few of
those are already pull requests against it.)

When the spec and the clients disagree, proof sides with the clients. For
instance, the spec recommends returning the ops in `describe` as a list, but
CIDER and Calva both expect a map. A server that follows the spec to the
letter, but that CIDER can't connect to, is not much use to anyone. That's
also why proof is a *compatibility* suite and not a *conformance* suite. You
can only conform to an official spec, and nREPL doesn't have one yet.

## Checking Servers

Checking servers is proof's main job. It's a single binary[^1] that talks to
your server over a socket, the same way any client would. It knows nothing
about the server's internals and it doesn't care what language the server is
written in. What it does need is a profile - a small TOML file that tells
proof how to start your server and gives it a few snippets of code in the
server's language (an idea I borrowed from
[jupyter_kernel_test](https://github.com/jupyter/jupyter_kernel_test)):

```toml
name = "My server"

[launch]
command = ["my-server", "--port", "0"]
port-pattern = 'nREPL server started on port (\d+)'

[snippets.value]
code = "(+ 1 2)"
value = "3"
```

Then you install proof and run it:

```shell
$ go install github.com/nrepl/proof/cmd/proof@latest
$ proof run my-server.toml
```

proof starts your server, runs about 80 checks against it and shuts it down
afterwards. (You can also grab a binary from the
[releases](https://github.com/nrepl/proof/releases), if you don't have Go
around.) Profiles for nREPL, Babashka, nbb, Basilisp, ClojureCLR, jank,
let-go, dialtone and repartee come with proof, so `proof run babashka` just
works if you have Babashka installed.

The most important decision in proof's design is how it grades the checks. A
check fails only when some client won't work properly with your server (or
when users will lose data), and the report tells you which client it is and
links to the client code in question, pinned to a specific commit. If your
server simply does something differently from the reference implementation,
you get a warning instead. Here's a failure from a run against Babashka
1.13.226:

```
FAIL   interrupt.other-connection   An interrupted eval ends on its own connection
       the interrupted eval's done went to the connection the interrupt came from
       why: neorepl interrupt stops an eval that another neorepl process
       started, and that process waits for the eval's done on its own
       connection forever if it goes anywhere else.
       see: neorepl interrupt stops an eval from another process
       https://github.com/nrepl/neorepl/blob/462a182c.../src/neorepl/main.clj#L374-L385
       see: nrepl/nrepl#498 https://github.com/nrepl/nrepl/issues/498
```

The idea is that a failure should always be worth fixing and never be a
matter of taste. If you think some failure isn't justified, the links show you
exactly what you need to argue with.

Some checks go a step further and send exactly what a particular client
sends, the way it sends it. `cider.connect` sends the requests CIDER sends
while connecting, `calva.eval` evaluates code the way Calva does, and so on
for Conjure, vim-fireplace, REPLy, neorepl, clojure-cli.repl and
`bb repl --connect`. That way the report tells you directly whether those clients will
work with your server.

Once your server passes everything it can pass, you can list the failures you
can't fix yet as expected failures in its profile and run proof in your CI
with its [GitHub Action][usage-ci]. When an expected failure starts passing,
proof fails the build and asks you to remove it from the list, so the list
can't go stale.

Last, but not least, the bundled profiles run every day and their reports end
up in a [compatibility matrix](https://nrepl.github.io/proof/), which shows
where the different servers agree, where they don't and where the spec needs
some work.

## Checking Clients

Servers were always proof's primary focus, but once the foundation was in
place people started asking me how they could check their clients with it.
That made a lot of sense to me - after all, the same knowledge cuts both ways.
proof has two tools for client authors.

`proof proxy` sits between a client and a server, passes everything along as
is and records it on the way. You use your client for a while (or run its
test suite through the proxy), press Ctrl-C and proof grades every request
the client sent. Here's a quick `neorepl eval` going through it:

```shell
$ proof proxy -listen 127.0.0.1:7888 babashka
$ neorepl eval --port 7888 '(println "hi") (+ 1 2)'
```

```
client
  PASS   client.bencode               Every frame is valid bencode
  PASS   client.dict                  Every request is a dict
  PASS   client.canonical             Frames use canonical bencode
  PASS   client.id                    Every request has an id
  PASS   client.active-id             An id isn't reused while its request is active
  PASS   client.field-types           Request fields have the right types
  PASS   client.required-fields       Requests carry the fields their op needs
  PASS   client.need-input            need-input is answered with stdin in the same session
  WARN   client.close                 Sessions are closed before disconnecting
  PASS   client.unknown-op            Requests use ops the server supports

9 passed, 0 failed, 1 warnings, 0 skipped
```

The grading rule is the same, just turned around. A client check fails only
when some server breaks, and it links to the server code in question. (That
warning is fine, by the way. neorepl keeps its session around on purpose, so
the next `neorepl eval` can use it.)

A proxy sees only what goes over the wire, though, so it can't tell you how
your client copes with the replies of different servers. That's what
`proof serve` is for. It's a small nREPL server for client test suites, which
behaves like nREPL unless you ask it to behave like some other server in some
particular way - send only the value of the last form (like Basilisp, jank and
dialtone), drop what the code prints to stderr, have no `interrupt` op,
deliver its replies a byte at a time, and so on. None of those scenarios are
made up. Every one of them is something a real server does (or something TCP
can do to any server's replies). `-like` turns on all the quirks of a server
at once, so you can run your client's tests against "jank" without installing
jank:

```shell
$ proof serve -listen 127.0.0.1:7888 -like jank
```

## proof in Practice

So far, so good. But does it actually find anything? You bet! Here are some
of the things proof turned up in the past couple of months:

- nREPL itself sent the end of an interrupted eval to the connection the
  interrupt came from (fixed on master), drops the evals waiting behind an
  interrupted one ([#503](https://github.com/nrepl/nrepl/issues/503)) and
  can leave a session that stops sending `done` after its client goes away in
  the middle of an eval ([#509](https://github.com/nrepl/nrepl/issues/509)).
  Oops!
- Babashka, nbb, Basilisp, jank and ClojureCLR all had their share of rough
  edges, e.g. errors without an `eval-error` status, sessions sharing `*1` and
  the current namespace, and a request that throws taking its whole connection
  down.
- CIDER skipped part of its setup when connecting to a server without
  cider-nrepl, Calva and Conjure didn't sort the keys of the dicts they sent, vim-fireplace
  and REPLy never close their sessions, and Conjure's interrupts don't say
  which eval to stop.

Michiel Borkent fixed the Babashka and nbb issues the same day I reported
them (or the next one), Conjure fixed its encoder the next day and Calva
within a week. That's the Clojure community for you!

I was also super happy to see other people picking up proof before I even got
around to announcing it. Tom Waddington has been running it against his nREPL
implementations for [Janet](https://github.com/waddie/nrepl-janet) and
[Steel Scheme](https://github.com/waddie/nrepl-steel) since early October
(nrepl-janet even tests its client with `proof serve`), Jeaye Wilkerson
[suggested](https://github.com/jank-lang/jank/issues/1104) using it for
jank's nREPL server, and Kyle Cesare is already
[fixing](https://github.com/jank-lang/jank/pull/1175) what it found there.
That's exactly what I was hoping for!

The projects that benefited from proof the most were my own, though. This
year I built a few toy nREPL implementations, mostly for fun and to see how
well the protocol holds up outside of Clojure:

- [nrepl-beam](https://github.com/nrepl/nrepl-beam) has nREPL servers for
  Erlang (dialtone) and Elixir (repartee). proof found that they didn't answer
  `close` with `session-closed`, didn't echo unknown ops and returned only the
  last value of several forms, and fixing those uncovered a race after `close`
  and temporary sessions that never stopped.
- [mezcaml](https://github.com/nrepl/mezcaml), my little OCaml client, read
  replies as if only one request was ever in flight, so late output from one
  eval could end the next one early.
- [neat](https://github.com/nrepl/neat), my language-agnostic client for
  Emacs, got late output, errors without `err` and unknown sessions wrong.
- [nrepl-sci](https://github.com/nrepl/nrepl-sci), the nREPL server behind
  the "Try it" section of nrepl.org, was the first server I wrote with proof
  around from day one, and [working on
  it]({% post_url 2026-11-01-nrepl-right-in-your-browser %}) mostly came down
  to running proof after every change.

And then there's
[neorepl]({% post_url 2026-10-10-neorepl-a-terminal-repl-for-clojure-and-friends %}),
the CIDER-inspired terminal REPL I've been working on lately, which is just as
happy to be used by coding agents as by humans. neorepl and proof pretty much
grew up together. I ran neorepl through `proof proxy` and every scenario of
`proof serve`, which caught a few bugs before neorepl 0.1 was out, and proof
checks every server with the exact requests neorepl sends. It's also how I
found the interrupt bugs in nREPL above, which hit neorepl harder than most
clients. Its commands are separate processes sharing a session, so
`neorepl interrupt` stops an eval that some `neorepl eval` started, and that
`neorepl eval` used to wait for its `done` forever.

## A Tool for Tool Authors

I guess I should spell out who proof is for. If you're using nREPL through CIDER
or Calva you'll probably never run proof yourself (although hopefully you'll
feel its effects). proof is a tool for tool authors - people writing nREPL
servers, clients and middleware.

I really hope that it will make it a lot easier to grow the nREPL ecosystem.
These days anyone can put together an nREPL server or client in no time with
the help of tools like Claude. Writing the code is no longer the hard part -
knowing whether the result actually works with CIDER, Calva and friends is.
proof gives you (and your coding agent) a fast feedback loop and failures that
explain themselves: what went wrong, why it matters and which client code
depends on it. That's usually all an agent needs to fix the problem.

## Why "proof"?

Naming is hard! Luckily, the nREPL world has a tradition that makes it a bit
easier, as many nREPL clients are named after drinks (CIDER, neat, mezcaml,
chaser and so on). proof is not a client, so it bends the rule a bit, but it still
belongs at the bar. The proof of a spirit is a measure of its strength, which
is more or less what this tool measures for nREPL servers. And running it
gives you proof that your server works with the existing clients (or a list of
the reasons why it doesn't). Two meanings for the price of one![^2]

## Epilogue

proof is still in its early days, but it already covers the core of the
protocol, the exact requests of the most popular clients and the server quirks
clients have to deal with. Now it needs more users.

So, if you're working on an nREPL server or client, please take proof out for
a spin and let me know how it went! I'd love to see your server in the
compatibility matrix, and if you think some failure isn't fair, open an issue
and let's argue about it. And if you're a casual Clojure hacker who'd just like
a nice REPL in the terminal, give neorepl a try:

```shell
$ bbin install io.github.nrepl/neorepl
```

Big thanks to Clojurists Together for supporting my work on nREPL, to Phil
Hagelberg for getting the spec going, and to the maintainers of all the
servers and clients who were so quick to fix the problems proof found. You
rock! In the (n)REPL we trust!

That's all I have for you today. Keep hacking!

[release]: https://github.com/nrepl/proof/releases/tag/v0.2.0
[spec-changes]: https://github.com/nrepl/proof/blob/main/doc/spec-changes.md
[usage-ci]: https://github.com/nrepl/proof/blob/main/doc/usage.md#running-proof-in-ci

[^1]: Written in Go, of all things. A Clojure tool would have been the obvious choice, but proof shouldn't favour any of the languages it checks, and a single binary without a runtime is easy to drop into any CI.
[^2]: "hangover" would have been a good name for what you get when a server forgets to send `done`, but it's a hard sell for a project.
