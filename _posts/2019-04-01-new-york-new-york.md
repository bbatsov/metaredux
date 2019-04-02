---
layout: post
title: New York, New York
date: 2019-04-01 15:27 +0200
categories: posts
tags:
- Clojure
- Emacs
- CIDER
---

> Start spreadin' the news, I'm releasin' today <br/>
> I want to be a part of it <br/>
> New York, New York <br/>
> These vagabond forms, are longing to eval <br/>
> Right through the very REPL of it <br/>
> New York, New York...

Following the long overdue overview of [nREPL 0.6]({% post_url
2019-03-29-nrepl-0-6 %}) I've got a new one for you!
Brace yourselves for an overdue overview of the recently released CIDER 0.21 (New York)!

It terms of volume of changes it's a pretty small release. If you
consider their impact, however, you'll see it packs quite the punch!
In fact it's probably one of the most significant releases CIDER has
had in the past couple of years.

<!--more-->

## Printing Improvements

You probably never saw this coming, but CIDER is now the first nREPL
client that makes use of the new streaming printing functionality
introduced in nREPL 0.6!

In practical terms this means that when dealing with a big value you'll
see it printed in chunks (meaning faster feedback cycle) and you'd be
able to interrupt the print process (e.g. with `C-c C-c` if you're in
a REPL buffer). No more REPLs will be sacrificed to the evil Lord of Infinite data
structures!

CIDER also introduced a configuration option to limit the total size
of a printed value regardless of the values of `*print-length*` and
friends.  Its name is `cider-print-quota` and its default is
1MB. Setting it to `nil` will disable it.

We also used the opportunity to clean up and consolidate the print-related configuration options:

* `cider-pprint-fn` and `cider-pprint-options` are now obsolete, replaced by `cider-print-fn` and `cider-print-options`.
* `cider-debug-print-options`, `cider-stacktrace-print-options`, and `cider-repl-pretty-print-width` are now all obsolete, replaced by `cider-print-options`.

`cider-nrepl` bundles print functions based on `clojure.pprint`,
`fipp`, `puget` and `zprint`, that you can use out of the box with
nREPL's new print middleware. You can easily define your own as
well. Here's one of the wrappers so you can get an idea about how
basic they are:

``` clojure
(defn fipp-pprint
  ([value writer]
   (fipp-pprint value writer {}))
  ([value writer options]
   (binding [*out* writer]
     (fipp.edn/pprint value options))))
```

Basically you need a function which accept the value to print, a
writer instance and a map of options controlling the printer.

By default printing in the REPL is now done with `clojure.print`.
Interactive evaluation results are not pretty-printed, as multi-line
results don't look very nice in the minibuffer or inline.

## Custom REPL Initialization Code

We've introduced the configuration option
`cider-repl-init-code`. This is a list of strings containing Clojure
code to evaluate when the REPL starts (with bindings for any
`set!`-able vars in place). It replaces `cider-print-length` and
`cider-print-level`, which are now obsolete. The new option is more flexible
and can be used for any forms you'd like to run when starting a new REPL:

``` emacs-lisp
(add-to-list 'cider-repl-init-code "(set! *print-length* 1000)")
(add-to-list 'cider-repl-init-code "(set! *print-level* 10)")
(add-to-list 'cider-repl-init-code "(my-favourite-repl-init-logic)")
```

Be careful how exactly you modify this list, as it contains by default
the code requiring Clojure's REPL utility functions like `doc` and
`source`, namely:

``` clojure
(clojure.core/apply clojure.core/require clojure.main/repl-requires)
```

That's why I suggested the use of `add-to-list`, although you can edit
the list however you prefer.

## Faster REPL

CIDER's REPL has become notorious when it comes to dealing with lots
of output. This release takes a few small steps to reduce the REPL
performance issues.

Prior to version 0.21.0, the REPL buffer would be automatically
re-centered whenever any output was printed, so that the prompt was on
the bottom line of the window, displaying the maximum possible amount
of output above it. Turned out, however, this had a horrible impact on
performance and now is no longer the default behaviour. You can
replicate the old behaviour by setting the built-in Emacs option
`scroll-conservatively`, for example:

``` clojure
(add-hook 'cider-repl-mode-hook '(lambda () (setq scroll-conservatively 101)))
```

Extensive profiling sessions of printing large results at the REPL helped identify
a couple of easy wins around excessive object allocation (we used to spend a
surprising amount of time in GC when the REPL was printing):

* Reuse the same buffer in `nrepl-bdecode` rather than creating a new one
each time.
* Combine calls to `replace-regexp-in-string`.
* Ensure we always insert output before the REPL prompt.

It's funny how several trivial tweaks can amount to pretty solid
performance gains overall!

## Grab Bag

Probably some of you already knew that evaluations weren't the only
operations you could interrupt in CIDER. For a very long time you
could use the same command (`cider-interrupt`) to interrupt test runs
in progress as well. CIDER 0.21 extended this to all
`cider-ns-refresh-*` commands.

You can find a complete list of all changes in the [release
notes](https://github.com/clojure-emacs/cider/releases/tag/v0.21.0).

## Upgrade Notes

The good news is that this time around we broke fewer things than usually, so this section is going to be pretty short!

CIDER 0.21 (New York) requires nREPL 0.6 to work properly. If you rely
on `cider-jack-in` probably you won't need to do anything besides
upgrading the CIDER package. If you rely on `cider-connect` you'll
need to make sure you're connecting to an nREPL 0.6 server:

* Lein users should upgrade to Lein 2.9+.
* Boot users will need to put nREPL 0.6 in their Boot profiles or project dependencies.
* Piggieback needs to be at version 0.4+ to work properly with nREPL 0.6.
* The optimal `cider-nrepl` version is 0.21.1.
* CIDER no longer sets any defaults for `*print-length*` and `*print-level*`. They were mostly replaced by the new `cider-print-quota`.

## Team Updates

I'm really glad to share the news that [Michael
Griffiths](https://github.com/cichli) came out of retirement to make
CIDER great (again)!

Michael had been a key CIDER contributor for several years now. He's
behind much of CIDER's ClojureScript support and also worked on cool
features like CIDER's reloaded workflow and the pretty-printing
functionality we had prior to nREPL 0.6. He was away from CIDER for a
couple of years and his absence was strongly felt, as without him
things really stagnated on the ClojureScript side. It's really great
to have him back!

Michael drove the entire CIDER 0.21 from start to finish and did an
exceptional job! Buy him a cider or 5 if you happen to meet him!

## (next CIDER)

I'm still ruminating on the roadmap for the next CIDER release. Most
likely it will be fairly small and won't feature more than 2-3
new features. Some shortlisted candidates are:

* Extend the functionality available without `cider-nrepl` (see [here](https://github.com/clojure-emacs/cider/issues/2611))
* Ability to hot-load a dependency straight from CIDER (replacement for a functionality that used to live in refactor-nrepl, but got broken with Java 9)
* Merge orchard and cljs-tooling together (see [here](https://github.com/clojure-emacs/orchard/pull/37))

In general I still believe that the biggest short-term gains are going
to come from further improvements of nREPL itself, so I'll likely keep my focus there
for the foreseeable future.

There are also quite a few tickets that have piled up - enhancement
requests, bug reports, etc. At some point I have to sift through all
of them and decide which we're are going to eventually tackle.  It's
pretty depressing to have 160 open tickets on your issue tracker!

If you're looking for a simple way to help out you can certainly
participate in the issue grooming process!

## Epilogue

> If I can eval it there <br/>
> I'm gonna eval it anywhere <br/>
> It's up to you, New York, New York

It feels awesome to see that in the time I was away CIDER made such a
bit leap forward. This speaks volumes to the health of the project and
shows that clearly CIDER can progress even without me.

Now let's give a big round of applause for the man of the hour
(release?) - Michael! You're awesome and we love you!

That's all from me, folks! Drink CIDER (responsibly), code long,
have fun, and prosper!
