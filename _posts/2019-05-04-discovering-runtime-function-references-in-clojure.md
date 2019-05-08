---
layout: post
title: Discovering Runtime Function References in Clojure
date: 2019-05-04 16:11 +0300
category: posts
tags:
- Clojure
- Orchard
- CIDER
---

Cognitect recently released a [new version of
REBL](https://www.patreon.com/posts/rebl-0-9-157-var-26478905) that
has a cool "find function references (usages)" feature. Rich was kind
enough to answer some questions about the new feature's implementation
on Slack, and he even shared one central bit of it - namely the
`fdeps` function that discovers the runtime dependencies of any
function:

``` clojure
(defn fdeps [val]
  (set (some->> val class .getDeclaredFields
                (keep (fn [^java.lang.reflect.Field f]
                        (or (and (identical? clojure.lang.Var (.getType f))
                                 (java.lang.reflect.Modifier/isPublic (.getModifiers f))
                                 (java.lang.reflect.Modifier/isStatic (.getModifiers f))
                                 (-> f .getName (.startsWith "const__"))
                                 (.get f val))
                            nil))))))
```

How does this work in practice? Here's a simple example:

``` clojure
(defn foo []
  (map inc (range 10)))

(fdeps foo)
;; => #{#'clojure.core/map #'clojure.core/range #'clojure.core/inc}
```

Basically, you get a set of all the vars that are used (depended on)
in the body of the target function. There are few things to keep in
mind:

* This function relies on Clojure implementation details (although it's unlikely those are going to change).
* You're relying on runtime information, which means the function you're analyzing must be loaded and you won’t see inlined functions or macros.
* `fdeps` operates on function values, not vars or symbols (although it's trivial to make it more generic).
* You'll get only the direct dependencies of the function. In other
  words - you won't get any transitive dependencies (including the dependencies of
  nested lambdas).

``` clojure
(defn bar [x]
  (map #(+ % 1) (range 10))

(fdeps foo)
;; => #{#'clojure.core/map #'clojure.core/range}
;; Notice that #'clojure.core/+ is missing.
```

You might be wondering what good is such a function, and more importantly - how does it
relate to something like "find references", which admittedly is way more useful.
While I don't know how exactly this functionality is implemented in REBL, I can share with you
a very simple implementation of "find references" that's built on top of the `fdeps` idea:

``` clojure
(defn all-vars []
  ;; definition omitted
  ;; Returns a list of all currently loaded vars.
  )

(defn frefs
  [var]
  (let [all-vars (all-vars)
        all-vals (map var-get all-vars)
        deps-map (zipmap all-vars (map fdeps all-vals))]
    (map first (filter (fn [[k v]] (contains? v var)) deps-map))))
```

And here's how to use it:

``` clojure
(frefs #'fdeps)
;; => (#'some.ns/frefs)

(frefs #'clojure.core/map)
;; => a ton of results
```

Now that's something pretty useful! I don't know about you, but I've
often resorted to `grep` and friends when I needed to find where some
function has been used, and `frefs` is definitely is more robust than
grep (at least when it comes to finding false positives).
Notice that I've conveniently omitted the implementation of `all-vars`, so we can
keep our focus on `frefs`.[^1] You can find some slightly more
sophisticated implementations of both `fdeps` and `frefs` in
[Orchard](https://github.com/clojure-emacs/orchard/blob/master/src/orchard/xref.clj).
This leads me to the fun part...

Those functions are going to be used for a new nREPL middleware (part
of `cider-nrepl`) that's finally going to provide some form of "find
references" in CIDER out-of-the-box.[^2] And this actually brings me
to the reason why I wrote this blog post in the first place.  It has
been long believed that the best (only?) way to approach this type of
functionality is by doing static analysis of the code.  This is a good
approach in general, although it comes with some added complexity
stemming from the fact you're no longer relying simply on REPL state
introspection (e.g. you have to constantly keep your code and its AST
in sync). There's nothing bad with that approach, but as far as I'm
concerned it's great to be able to provide a really useful form of
"find references" that's trivial in nature, pretty fast and
REPL-powered. Sure, it's not super reliable (as noted above), but it
yields good results and it plays well with the whole idea of
REPL-powered introspection that tools like CIDER are built upon.

I already have a running prototype of "find references" in CIDER based
on the code I've shared in this post and it's looking pretty sweet so
far.  I hope I'll have something to share with everyone on the subject
next week. Until then - keep hacking! May the Source be with you!

P.S. It'd be really great if someone figured out how to do this for
ClojureScript as well. I bet there must be a way to implement
something similar using only the compiler's state.

[^1]: You can find a real implementation in Orchard's [orchard.query](https://github.com/clojure-emacs/orchard/blob/master/src/orchard/query.clj) namespace.
[^2]: Currently you can get this type of functionality from `clj-refactor.el` and `refactor-nrepl`.
