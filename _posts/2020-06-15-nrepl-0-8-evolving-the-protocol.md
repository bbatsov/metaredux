---
layout: post
title: 'nREPL 0.8: Evolving the Protocol'
date: 2020-06-15 11:57 +0300
tags:
- Clojure
- nREPL
---

A few days ago I released the [first alpha
version](https://clojars.org/nrepl/versions/0.8.0-alpha1) of what is eventually
going to become nREPL 0.8.  The upcoming release is quite special, as it adds a
couple of additional ops to the core nREPL protocol itself - namely
`completions` and `lookup`. Apart from this it extends the sideloader
functionality, introduced in nREPL 0.7, to allow for dynamic loading of
middleware.

One of the great things about nREPL has always been its simplicity. Implementing both nREPL servers and clients is
pretty easy (as they the protocol is simple) and eventually nREPL gained traction even outside of the Clojure community, which gave birth to it.[^1]
While the default set of nREPL ops cover all basic use-cases[^2], there are many other common use-cases - e.g. code completion,
looking up definition and documentation, finding usages, etc. The Language Server Protocol gained a lot of traction in
recent years, partly because of its broader API that can power out-of-the-box most programming editors.

In the world of Clojure this void has historically been filled by third-party
nREPL middleware (e.g. `cider-nrepl` and `refactor-nrepl`), but there are always merits to having a
more powerful out-of-the-box experience. That's why after some (a lot of) consideration
I've decided to extend the protocol with an op for code completion
(`completions`) and another that resolves a symbol and looks up as much data for
it as possible (`lookup`). Both those ops make sense for pretty much every programming language and therefore
they fit with nREPL's philosophy.

Let's examine the new ops.

<!--more-->

## `completions` op

The API of that op is based on that of the similar `complete` that has existed in `cider-nrepl` for ages. Here's a typical
`completions` request and its response:

``` clojure
;; request
{:op "completions" :ns "some.ns" :prefix "map"}

;; response
{:completions [{:candidate "map", :ns "clojure.core", :type :function}
               {:candidate "map-entry?", :ns "clojure.core", :type :function}
               {:candidate "map-indexed", :ns "clojure.core", :type :function}
               {:candidate "map?", :ns "clojure.core", :type :function}
               {:candidate "mapcat", :ns "clojure.core", :type :function}
               {:candidate "mapv", :ns "clojure.core", :type :function}]
 :status #{"done"}}
```

The implementation is pretty simple and won't yield results that are as good of those coming from `cider-nrepl`,
but it gets the job done, and it does so without any external deps, which is a hard requirement for nREPL.
By default the `completions` op will use nREPL's own
code completion in `nrepl.util.completion`, but you can instruct nREPL to use
another completion function (e.g. one based on `compliment`).

The completion function should accept 3 parameters:

* `prefix`: The completion prefix.
* `ns`: The namespace in which to look for completion candidates.
* `options`: A map of additional options.

The function should ideally return results in the following format:

``` clojure
[{:candidate "map", :ns "clojure.core", :type :function}
 {:candidate "map-entry?", :ns "clojure.core", :type :function}
 {:candidate "map-indexed", :ns "clojure.core", :type :function}
 {:candidate "map?", :ns "clojure.core", :type :function}
 {:candidate "mapcat", :ns "clojure.core", :type :function}
 {:candidate "mapv", :ns "clojure.core", :type :function}]
```

You can either specify a custom completion function in the requests (via the `complete-fn` key) or by
setting `nrepl.middleware.completion/*complete-fn*`.

``` clojure
user=> (set! nrepl.middleware.completion/*complete-fn* my.namespace/my-completion)
```

**Note:** The name of the op is not exactly aligned with all other protocol ops,
which are typically verbs - e.g.  `clone`, `eval`, etc. The main reason for this
is to avoid a conflict with `cider-nrepl` which has been using the `complete`
name for years. Down the road, probably `complete` will make it to the protocol
as an alias for `completions`. Backwards compatibility is tricky. If only I had
the foresight to namespace the `cider-nrepl` ops (e.g. `cider/complete`)...

## `lookup` op

The main purpose of the `lookup` op is to provide the necessary data for clients
to implement features like go to definition and displaying documentation. It
simply resolves a symbol in the context of the current namespace, and returns
all the metadata it can obtain for it. The op is inspired by the `info` op from
`cider-nrepl`. Here's how using it looks like:

``` clojure
;; request
{:op "lookup" :sym "map" :ns "some.ns"}

;; response
{:info {:added "1.0",
        :ns "clojure.core",
        :name "map",
        :file "jar:file:/Users/bozhidar/.m2/repository/org/clojure/clojure/1.10.1/clojure-1.10.1.jar!/clojure/core.clj",
        :static "true",
        :arglists-str "([f] [f coll] [f c1 c2] [f c1 c2 c3] [f c1 c2 c3 & colls])",
        :column 1,
        :line 2727,
        :arglists ([f] [f coll] [f c1 c2] [f c1 c2 c3] [f c1 c2 c3 & colls]),
        :doc
        "Returns a lazy sequence consisting of the result of applying f to\n  the set of first items of each coll, followed by applying f to the\n  set of second items in each coll, until any one of the colls is\n  exhausted.  Any remaining items in other colls are ignored. Function\n  f should accept number-of-colls arguments. Returns a transducer when\n  no collection is provided."}
 :status #{"done"}}
```

I'm still on the fence about the name of the return key - e.g. `info`, `lookup-info`, etc.

By default `lookup` will use nREPL own
symbol lookup logic in `nrepl.util.lookup`, but you can instruct nREPL to use
another lookup function (e.g. `info` from `orchard`). This is pretty similar to what I outlined earlier about
`completions`.

The lookup function should accept 2 parameters:

* `ns`: The namespace in which to look for completion candidates.
* `sym`: The symbol to lookup.

The function should ideally return results in the following format:

``` clojure
{:added "1.0",
 :ns "clojure.core",
 :name "map",
 :file
 "jar:file:/Users/bozhidar/.m2/repository/org/clojure/clojure/1.10.1/clojure-1.10.1.jar!/clojure/core.clj",
 :static true,
 :arglists-str "([f] [f coll] [f c1 c2] [f c1 c2 c3] [f c1 c2 c3 & colls])",
 :column 1,
 :line 2727,
 :arglists ([f] [f coll] [f c1 c2] [f c1 c2 c3] [f c1 c2 c3 & colls]),
 :doc
 "Returns a lazy sequence consisting of the result of applying f to\n  the set of first items of each coll, followed by applying f to the\n  set of second items in each coll, until any one of the colls is\n  exhausted.  Any remaining items in other colls are ignored. Function\n  f should accept number-of-colls arguments. Returns a transducer when\n  no collection is provided."}
```

NOTE: nREPL's default lookup function is pretty basic and it can only handle symbols that resolve to special
forms and vars. This might change down the road, but it's definitely a non-goal to try to replicate the behaviour
of `cider-nrepl`'s `info` op.

You can specify a custom lookup function in the requests (via the `lookup-fn` key) or by
setting `nrepl.middleware.lookup/*lookup-fn*`.

``` clojure
user=> (set! nrepl.middleware.lookup/*lookup-fn* my.namespace/my-lookup)
```

## Dynamic Middleware Loading

**Note:** That section is specific to the Clojure nREPL implementation.

nREPL 0.8 includes a `dynamic-loader` middleware, which can be used, at runtime,
query and change the middleware stack the nREPL server is using. This is especially
powerful when combined with sideloading, as it allows a client to configure the
server after connecting, and provides an alternative to having to specify the middleware required by the client as startup time.

This introduces three new operations:

* `ls-middleware`, to return a list of active middleware, ordered from inside outwards.
* `add-middleware`, which adds a middleware to the stack. Optionally, a list of `extra-namespaces` could be provided for loading. This is useful when adding middleware that implement some form of deferred loading. Examples include `cider-nrepl` and `refactor-nrepl`. In these cases, some of the required namespaces might only be loaded upon first use, which may occur outside of a sideloading session, and thus fail. This feature allows us to pre-load namespaces when we add a middleware. If loading of any particular middleware fails, the stack will be unchanged.
* `swap-middleware`, similar to `add-`, but replaces all existing middleware. Note that this _may_ remove the `dynamic-loader` itself.

It practical terms this means that now clients can simply bundle whatever middleware they need to power their features (e.g. `piggieback`, `cider-nrepl`, `refactor-nrepl`, `sayid`) and load this middleware into an nREPL server after connecting to it.
That's immensely powerful and will eventually result is no need for user setup to get access to everything a client has to offer. You'll forget about messages like `CIDER requires cider-nrepl for some its functionality to work properly`.
It's going to take us a while to get to the point where most Clojure clients support this, but it's going to be a great moment when we get there.

Big thanks to Shen Tian for tackling this!

## Closing Thoughts

At this point nREPL 0.8 is mostly feature complete. There are certain aspects of the new APIs that need to be polished, though,
and I'm looking forward to hearing some feedback on them. I'm hoping we'll get to release nREPL 0.8 in the next couple of months.

It's important to understand that new ops (sans those related to middleware
loading) don't aim to replace functionality in `cider-nrepl`, but rather extend
the protocol and improve the baseline nREPL experience. The impact of those
changes will be felt best outside the realm of Clojure, where some solution like `cider-nrepl` is not currently available.

The next step for me at this point is to add support for the new nREPL functionality to CIDER. It already supports `completions` and adding support for `lookup` will be pretty
straightforward as well, but I'll have to do a bit more work on the sideloader/dynamic middleware loading front.

As usual - I'd like to extend special thanks to [Clojurist Together](https://www.clojuriststogether.org/) for funding my recent work on nREPL and CIDER! The projects wouldn't be the same
without their support!

[^1]: You might want to read about the nREPL protocol vs the Clojure nREPL server [here]({% post_url 2019-01-12-nrepl-beyond-clojure %}).
[^2]: In the words of the Beatles - "All you need is eval...".
