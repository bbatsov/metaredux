---
layout: post
title: Understanding nREPL Sessions
categories: posts
tags:
- Clojure
- nREPL
- Meta Advent 2019
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

nREPL has many interesting aspects of its design and its notion of sessions
is definitely one of those. In this article I'll try to shed some light on sessions
and explain their usefulness.

**Note:** If some of the terminology in this article (e.g. nREPL messages) is unclear to you,
please consult nREPL's [design documentation](https://nrepl.org/nrepl/0.6.0/design/overview.html) for
a quick overview of the key concepts.

Simply put, nREPL sessions allow you to multiplex multiple "logical" (meta) REPLs over a single nREPL connection.
To illustrate this better - you can have two REPLs powered by nREPL using the same connection
with the dynamic bindings specific to each REPL (e.g. `*e`) contained in their sessions. That's pretty different from
the socket REPL that's bundled with Clojure, as there you'll need a separate physical connection
for each REPL you need. Both models have their pros and cons and I'll leave you to decide for yourself
which one is more to your liking.

Internally nREPL sessions persist [dynamic
vars](https://clojure.org/reference/vars) (collected by `get-thread-bindings`)
against a unique lookup. This allows you to have different values for vars like
`*e` from different REPL clients (e.g. two separate REPL-y instances) or from a
single client making use of multiple session within one connection. An existing
session can be cloned to create a new one, which then can be modified. This
allows for copying of existing preferences into new environments.

Each nREPL message (e.g. `eval`) is evaluated within a session, although you're not required to specify
the session id explicitly. Here's a typical evaluation message:

``` clojure
{:session "44d1978c-e2b5-4f86-a0d1-0f9f53e167bf" :op "eval" :code "(+ 1 2 3)"}
```

I hope it's easy to guess that this message will evaluate the code `(+ 1 2 3)`
in the context of the specified session. This evaluation will also update
vars like `*e`, `*1`, etc.

There are two types of sessions:
_ephemeral sessions_ and _long-lived sessions_ (or registered sessions).

**Ephemeral sessions** are used for one-off processing of a single message. If a message
arrives without a session id, one is created and assigned to it. This is discarded
after processing the message. It's a common practice dispatch non-eval nREPL ops
via such sessions.

**Long-lived sessions** provide two things, persistence of values between
messages, and a guarantee for serial execution of messages. The only way to
create a long-lived session is to clone an existing session (even an
ephemeral one). Cloning is achieved via the `clone` nREPL op - it can be used
both to create a "clean" new session or to clone an existing session.

Clojure development tools like CIDER make heavy use of sessions - e.g. each CIDER connection to
nREPL creates two nREPL sessions - one for the user's evaluations and one for
internal (tooling) operations (e.g. an interactive commands defined in terms of
evaluating some Clojure code).[^1]  Why is this necessary? Well, it's quite simple
actually - if we used only a single session, the tooling operations would mess up
vars like `*e`, `*1`, `*2`, etc. But there's actually more to this - each session is
internally backed by one evaluation thread and `eval` requests get serialized for
execution by this thread. By using the two sessions we also ensure that tooling
operations won't block user evaluation. For similar reasons CIDER dispatches
all nREPL ops, other than `eval`, to ephemeral sessions.

On a related topic - how would you approach the problem of needing to evaluate
many long-running operations (e.g. expensive computations) in parallel? Well,
you'll just spin all of those evaluations in different sessions. Transient
sessions are ideal for this type of tasks. Keep in mind that when it comes to
non-eval ops there's no serialisation guarantee with processing of messages in
ephemeral sessions (though they are serialized in the current implementation
since they run on the server IO thread).

By the way, have you ever thought how evaluation interruption works in nREPL?
Each time you interrupt a evaluation in nREPL you're killing its session thread and
a new one gets created in its place.

Sessions become even more useful when different nREPL extensions start
taking advantage of
them. [debug-repl](https://github.com/gfredericks/debug-repl) uses
sessions to store information about the current breakpoint, allowing
debugging of two things
separately. [piggieback](https://github.com/nrepl/piggieback) uses
sessions to allow host a ClojureScript REPL alongside an existing
Clojure one. I think Piggieback is my favourite example of how far
the simple session abstraction can take you.

That's all I have for you today! See you again tomorrow!

[^1]: Usually those serve as fallbacks for operations normally powered by cider-nrepl, when it's absent.
