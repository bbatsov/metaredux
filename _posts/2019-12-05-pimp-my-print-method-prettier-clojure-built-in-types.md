---
layout: post
title: 'Pimp my Print Method: Prettier Clojure Built-in Types'
date: 2019-12-05 11:27 +0200
categories: posts
tags:
- Clojure
- Meta Advent 2019
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

Trust me, it wasn't easy to find the right name for this article. I started off with the
pompous sounding "Improving the Printed Representation of Certain Clojure Built-in Object Types" and
needed half an hour to come to the current title. Naming is hard!

So, what's the fancy sounding title all about? Have you noticed that many Clojure types are not
represented in a particularly "user-friendly" way in the REPL?[^1] Here are a few examples:

```
Clojure 1.10.1
user=> +
#object[clojure.core$_PLUS_ 0x2bc12da "clojure.core$_PLUS_@2bc12da"]
user=> print-method
#object[clojure.lang.MultiFn 0x3122b117 "clojure.lang.MultiFn@3122b117"]
user=> (atom 5)
#object[clojure.lang.Atom 0x17ae7628 {:status :ready, :val 5}]
user=> (find-ns 'clojure.core)
#object[clojure.lang.Namespace 0x5b78fdb1 "clojure.core"]
```

Here we have a function, a multi-method, an atom and a namespace object. While when you're
used to Clojure their printed representations probably make sense to most people, they certainly
are confusing to newcomers. The representation of `+` in particular is pure gold when it comes
to clarity. The real question, however, is "Can we do better than this?". Yes, we can!

People using CIDER or editors backed by `cider-nrepl` (e.g. `vim-fireplace` and Calva) might be confused by the examples I gave,
because they'd get something pretty different in their REPLs. Let's do this one more time in CIDER's REPL:

``` clojure
user> +
#function[clojure.core/+]
user> print-method
#multifn[print-method 0x757ca063]
user> (atom 5)
#<Atom@3e319fc5: 5>
user> (find-ns 'clojure.core)
#namespace[clojure.core]
```

I don't know about you, but I find this version much easier to comprehend. So, time for the crux of this
article - how did we achieve this? It's pretty simple actually - we simply leveraged `print-method`, a built-in Clojure
multi-method that controls how an object gets printed. Here's an excerpt from the [actual implementation](https://github.com/clojure-emacs/cider-nrepl/blob/v0.22.4/src/cider/nrepl/print_method.clj):

``` clojure
(def ^:dynamic *pretty-objects*
  "If true, cider prettifies some object descriptions.
  For instance, instead of printing functions as
      #object[clojure.core$_PLUS_ 0x4e648e99 \"clojure.core$_PLUS_@4e648e99\"]
  they are printed as
      #function[clojure.core/+]

  To disable this feature, do
      (alter-var-root #'cider.nrepl.print-method/*pretty-objects* not)"
  true)

(defmacro def-print-method [dispatch-val arg & strings]
  `(defmethod print-method ~dispatch-val [~arg ~'^Writer w]
     (if *pretty-objects*
       (do ~@(map #(list '.write 'w %) strings))
       (#'clojure.core/print-object ~arg ~'w))))

(defn- translate-class-name [c]
  (main/demunge (.getName (class c))))

;;; Atoms
;; Ex: #atom[{:foo :bar} 0x54274a2b]
(def-print-method Atom c
  "#atom["
  (pr-str @c)
  (format " 0x%x]" (System/identityHashCode c)))

;;; Function objects
;; Ex: #function[cider.nrepl.print-method/multifn-name]
(def-print-method AFunction c
  "#function["
  (translate-class-name c)
  "]")
```

It's a bit more complicated than it needs to be, mostly because of the wrapper `def-print-method` macro that
makes each `print-method` aware of a dynamic var that controls the pretty-printing. It's needed in
a tool like CIDER, in case some users don't like the different printing, but I guess most people won't need this
if they're just tweaking something in their local setup.

All in all - you need this code in some namespace that gets required early-on in your application and you're done.
That's exactly what `cider-nrepl` does.
While writing this article I also realized that I should really move this code to `Orchard`, as it's not nREPL-specific
in any way, and having it as part of `Orchard` would make it easier for more people to reuse the code directly.

The technique is not without its flaws, though. Recently a CIDER user ran into the following problem:[^2]

``` clojure
(defrecord Foo []
  clojure.lang.IDeref (deref [x] x) )
(Foo.)
;; =>
;; 2. Unhandled clojure.lang.ExceptionInfo
;;
;; 1. Caused by java.lang.IllegalArgumentException
;;    Multiple methods in multimethod 'simple-dispatch' match dispatch value: class
;;    scratch.Foo -> interface clojure.lang.IDeref and interface
;;    clojure.lang.IPersistentMap, and neither is preferred
```

Solving the problem is as easy as adding:

``` clojure
(prefer-method print-method
  clojure.lang.IPersistentMap
  clojure.lang.IDeref)
```

Still, I do believe that implementing `IDeref` in a record is a bad idea, and
I'm also reasonably sure this is the only bug report we've got in the 5 years
since this feature was added to CIDER. This leads me to believe it works fairly
well for most people.

That's all I had for you today. I hope you managed to
learn something useful. See you tomorrow!

[^1]: For my definition of "user-friendly" I guess.
[^2]: This bug report was my inspiration to write the article you're now reading.
