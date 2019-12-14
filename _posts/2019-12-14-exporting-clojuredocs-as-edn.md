---
layout: post
title: Exporting ClojureDocs as EDN
categories: posts
tags:
- Clojure
- Meta Advent 2019
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

Remember how a while ago I wrote about the [demise of Grimoire]({% post_url 2019-06-29-farewell-grimoire %})? Eventually
we had to replace the Grimoire documentation lookup functionality in CIDER with similar functionality
powered by [ClojureDocs](https://clojuredocs.org/) and today I want to share
how exactly did we do this.

With Grimoire it was possible to query the service for a particular symbol,
which made it really easy to integrate it in CIDER. As Emacs can handle web
API requests directly, the entire integration was done Emacs-side. This worked
fairly well, but had the small downside that when you requested info about some
var from Grimoire it would take a second or so to fetch this data. I thought
a few times about building a smarter Grimoire middleware that did some data pre-fetching/caching,
but sadly I never found the time to work on this.

When we started working on a similar ClojureDocs integration for CIDER we
struggled a bit, as ClojureDocs doesn't have an equivaluent API.  This meant
that the only option for us was to simply export all the ClojureDocs data and query it
locally.  I've always been a bit bothered by the fact that ClojureDocs has only
a JSON data export. JSON is relatively easy to work with and quite ubiquitous
these days, but the Clojure community generally favours the use of EDN. And this
leads us to the crux of this article...

We didn't really want to deal with JSON, so [Masashi Iizuka](https://github.com/liquidz)
(of `vim-iced` fame) created a [simple
service](https://clojuredocs-edn.netlify.com/) that downloads fresh
data from ClojureDocs each day and converts it to EDN.  As a bonus the service provides
several versions of the data - a direct port of the original JSON data, a compact version that
removes all the data related to updates that were done to some entry, and a minified variant
of the compact version. Generally what we needed was the compact format, so here I'll share an example of it:

``` clojure
;; compact EDN
{:clojure.core/remove
 {:added "1.0",
  :ns "clojure.core",
  :name "remove",
  :file "clojure/core.clj",
  :static true,
  :type "function",
  :column 1,
  :see-alsos
  [:clojure.core/filter :clojure.core/group-by :clojure.core/keep],
  :line 2818,
  :examples
  ["(remove pos? [1 -2 2 -1 3 7 0])\n;;=> (-2 -1 0)\n\n(remove nil? [1 nil 2 nil 3 nil])\n;;=> (1 2 3)\n\n;; remove items that are evenly divisible by 3\n(remove #(zero? (mod % 3)) (range 1 21))\n;;=> (1 2 4 5 7 8 10 11 13 14 16 17 19 20)"
   ";; compare to filter\n\n(remove even? (range 10))\n;;=> (1 3 5 7 9)\n\n(remove (fn [x]\n  (= (count x) 1))\n  [\"a\" \"aa\" \"b\" \"n\" \"f\" \"lisp\" \"clojure\" \"q\" \"\"])\n;;=> (\"aa\" \"lisp\" \"clojure\" \"\")\n\n; When coll is a map, pred is called with key/value pairs.\n(remove #(> (second %) 100)\n       {:a 1\n        :b 2\n        :c 101\n        :d 102\n        :e -1})\n;;=> ([:a 1] [:b 2] [:e -1])\n"
   ";; remove items from a set/list\n\n(remove #{:a} #{:b :c :d :a :e})\n;;=> (:e :c :b :d)\n\n(remove #{:a} [:b :c :d :a :e :a :f])\n;;=> (:b :c :d :e :f)\n"
   ";; use map as a pred\n\n(remove {:a 42 :b 69} #{:a :b :c})\n;;=> (:c)"],
  :notes nil,
  :arglists ["pred" "pred coll"],
  :doc
  "Returns a lazy sequence of the items in coll for which\n  (pred item) returns logical false. pred must be free of side-effects.\n  Returns a transducer when no collection is provided.",
  :library-url "https://github.com/clojure/clojure",
  :href "/clojure.core/remove"}}
```

Basically you get a simple Clojure map where the keys are fully qualified names and the values are maps describing those names.
If you're interested in the implementation details you can check out the [GitHub repo](https://github.com/clojure-emacs/clojuredocs-export-edn).

If you're curious how CIDER interacts with this service you should check out [this namespace](https://github.com/clojure-emacs/orchard/blob/master/src/orchard/clojuredocs.clj) in Orchard. Basically Orchard fetches the data once and caches it, refreshing it every few days. It's not super elegant, but it gets the job done.
On the bright side, now once the data gets locally cached all the queries become instantaneous. We've also wrapped in this in a nREPL middleware (part of `cider-nrepl` as well).
Now it's really simple for tool authors to implement ClojureDocs lookup.

Potentially down the road we can extend the export service to provide some simple data query interface. If someone is looking
for some simple way to contribute to CIDER and its Orchard, that's as good of a starting point as any.
For me this story is a great example of the power of cross-team collaboration (in this case `vim-iced`'s and CIDER's) and sharing
a common foundation for building development tools upon.

That's all I have for you today! I hope that some of you will find our work on
the data export service and the ClojureDocs support in Orchard and `cider-nrepl`
useful! See you again tomorrow!
