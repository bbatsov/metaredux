---
layout: post
title: 'nREPL: Beyond Clojure'
date: 2019-01-12 17:01 +0200
category: posts
tags:
- nREPL
- Clojure
- Programming
---

## Prelude

Many people think of [nREPL](https://nrepl.org) as a Clojure-specific
REPL server and that's perfectly fine.  After all the project started
its life in the realm of Clojure and the canonical and most widely
used implementation is written in Clojure. However, nREPL is much more
than its reference implementation and a recent conversation with [Phil
Hagelberg](https://github.com/technomancy), reminded me of the
significance of this.

The default network protocol (socket-based communication with messages
encoded using [bencode](https://en.wikipedia.org/wiki/Bencode)) used
is simple, depending neither on JVM or Clojure specifics, thereby
allowing (*encouraging?*) the development of non-Clojure REPL clients.
The REPLs operational semantics are such that essentially any non-JVM
Clojure implementation should be able to implement it, with allowances
for hosts that lack the concurrency primitives to support
e.g. asynchronous evaluation, interrupts, etc.

<!--more-->

## Beyond Clojure

The validity of this assumption can be backed by the non-Clojure nREPL implementations that exist today:

* [HyREPL](https://github.com/Foxboron/HyREPL) - an nREPL for the [Hy programming language](http://hylang.org/)
* [JeeJah](https://gitlab.com/technomancy/jeejah) - an nREPL server for [Fennel](https://fennel-lang.org/) and [Lua](https://www.lua.org/)
* [Ogion](https://gitlab.com/technomancy/ogion) - an nREPL server for [Racket](https://racket-lang.org/)
* [Chicken nREPL](http://wiki.call-cc.org/eggref/5/nrepl) - an nREPL server for [Chicken Scheme](https://call-cc.org/)
* [cl-nrepl](https://github.com/sjl/cl-nrepl) - an nREPL server for Common Lisp
* [cljs-noderepl](https://github.com/bodil/cljs-noderepl) - an nREPL server for ClojureScript running on Node.js
* [nREPL CLR](https://github.com/clojure/clr.tools.nrepl) - an nREPL server for ClojureCLR

As you can see nREPL has implementations for many popular Lisp
dialects, but it's certainly not limited to Lisp dialects.

I can't speak to the maturity and the sophistication of any of those
implementations, but the fact that they exist, without any effort to
promote nREPL as an universal solution, speaks volumes to me.

It'd like to point out that there's a clear distinction between nREPL for Clojure and the *nREPL protocol*.
It's important to understand that the nREPL protocol is **language-agnostic** and can be
leveraged for many languages that have the ability to evaluate code
at runtime.

I think that one way to describe nREPL would be as something similar
in nature to the [Language Server
Protocol](https://langserver.org/). nREPL is not nearly as ambitious
as LSP, but on the other hand it's also much simpler and it's trivial
to create nREPL clients in any language. While JSON is quite
ubiquitous these days, I doubt many people would say that working with
JSON(-RCP) is a lot of fun.

Of course, the nREPL protocol is not bound to bencode and can be implemented in terms
of other data formats as well. [Fastlane](https://github.com/nrepl/fastlane) provides
a JSON and MessagePack transports for the default implementation and we're also working
on an EDN transport that will be bundled with it.

## The nREPL Protocol

Let's take a closer look at the nREPL protocol.

It consists of only a handful of operations (`eval` being the most
important of them) and it essentially an exchange of a simple request
and response maps. Ideally the requests should be processed
asynchronously, so they can be processed in parallel and interrupted
if needed. A code evaluation operation is as simple as:

``` clojure
;; Note: This example has been edited for simplicity.

;; request
{:op "eval"
 :code "(+ 1 1)"}

;; response
{:value 2}
```

If you exclude connection-related operations, the whole protocol can be reduced to just 3 operations:

* `eval` - Evaluate some code.
* `interrupt` - Interrupt the current eval operation.
* `load-file` - Load a complete source file.

Pretty generic, right?

As so much functionality can be implemented in terms of `eval`
(e.g. code completion, code lookup, etc) we never really felt a strong
need to extend the base protocol. To my knowledge it was never after
nREPL 0.2's release and there are no plans to extend it down the
road. It would never get some Clojure-specific op
(e.g. `macroexpand`) - that's for sure.

The protocol provides a simple `describe` op to inspect the server's
capabilities - all the operations supported by it.  Executing
non-built in operations is exactly the same as the executing built-in
ops, so people are free to extend the base protocol in whatever way
they see fit.

``` clojure
;; Note: This example has been edited for simplicity.

;; request
{:op "complete"
 :code "map"}

;; response
{:completion-candidates ["map" "map?" "mapv" "mapcat"]}
```

Many people wonder why nREPL uses bencode as its default data encoding - after
all arent' everyone using XML, JSON, MessagePack, etc? Those are all
great and extremely flexible data formats, but their flexibility
also brings a lot of complexity.[^1]

The bencode encoding simplifies the work for nREPL client writers,
as bencode is a **very simple** format. You can represent only four data
types there - maps, lists, strings and integers. That might sounds a
bit too limiting, but you can get pretty far with just this -
[CDER](http://www.cider.mx/) being a great example.

While the reference implementation supports the protocols's operations
using [middleware](https://nrepl.org/nrepl/design/middleware.html),
you're not really forced to adopt the same approach if you're building
an nREPL server yourself. You can just as easily have some switch-like
construct with all the supported ops and be done with it.

Another non-essential implementation detail is the asynchronous nature of the
message processing. While, I do believe that the asynchronous model works
pretty well, there's nothing stopping you from implementing an nREPL
server where messages are processed in a synchronous manner (although you'd
lose `interrupt` if you go down this road).

The semantics of the protocol matter, not the actual implementation of those semantics.

## A Good Client

One thing to keep in mind is that a good nREPL client should make no
assumptions about nREPL's implementation.  Unfortunately many clients
assume Clojure, which limits their usability with non-Clojure
nREPL implementations. Fundamentally this comes down to the fact that most
clients are not just clients, but also double as Clojure development
environments to some extent. Again CIDER would be a fine example here
(and that's on me) - while `nrepl-client.el`, which is at the heart of
CIDER, is a completely generic nREPL client, the client logic is
intertwined with some CIDER and Clojure-specific concepts, which make
the client unusable outside of CIDER.

I've long planned to cleanly decouple it, but alas - I never got to
this point.  On the bright side - that might be a fun weekend project
for one of you, my dear readers!

## Epilogue

I'd certainly love to see more nREPL implementations out there in the
years to come. nREPL definitely doesn't have the marketing prowess of a
company like Microsoft, or the ambitions of LSP, but it's a really
flexible and simple solution that can get you pretty far without a big
upfront investment.

The purpose of this post is not to extol the virtues and
advantages of nREPL - it is simply to show you something familiar in an
unfamiliar light. I always find this enlightening and I hope
you'll do so too!

Keep hacking!

[^1]: Especially XML.
