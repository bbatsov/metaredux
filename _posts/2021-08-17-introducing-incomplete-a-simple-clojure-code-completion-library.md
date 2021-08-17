---
layout: post
title: 'Introducing incomplete: A Simple Clojure Code Completion Library'
date: 2021-08-17 12:27 +0300
tags:
- Clojure
- nREPL
---

Today I've cut the first release of
[incomplete](https://github.com/nrepl/incomplete), a new Clojure code completion
library that aims to replace
[clojure-complete](https://github.com/ninjudd/clojure-complete). `incomplete`
was extracted from nREPL's codebase, so it's already battle-tested. So, why
bother with the creation of a new completion library if we already have
`clojure-complete` and the newer and much more capable
[compliment](https://github.com/alexander-yakushev/compliment)?

`clojure-complete` has several long-standing bugs and hasn't seen much love in recent years.
Still, the project is extremely popular due to its simplicity and the fact that it's
bundled with tools like Leiningen and REPLy.

`incomplete` started its life inside nREPL, as the provider of nREPL's built-in code
completion, but I decided it might be a useful standalone library as well.
It sits somewhere between `clojure-complete` and `compliment` in the sense that it has
more features (and less bugs) than the former, and it's much simpler and less capable than the
latter.

Here's a list of the `incomplete`'s advantages over `clojure-complete`:

* better completion of Java instance and static members
* keyword completion in Clojure
* candidate metadata (useful for tool authors)
* cleaner codebase (subjective, of course)

Like `clojure-complete`, `incomplete`'s entire implementation is in a single
namespace.  The long-term goal for the project is to replace `clojure-complete`
in REPLy and Leiningen. I hoped to replace it with `compliment` in the past, but
it turned out [this was not
feasible](https://github.com/trptcolin/reply/pull/153). That PR is now 7 years
old! Crazy, right? Still, I haven't given up!

`incomplete`'s name refers to its basic nature and modest goals.[^1]
And to the fact that there's still plenty of room for improvements, of course.
Feedback and patches are most welcome!
`incomplete` doesn't aim to compete with the gold standard for completion `compliment`, but it borrows a few ideas from it (e.g. candidate metadata).

Here are a few examples of using `incomplete`:

``` clojure
(require 'incomplete.core)

;; var completion
(completions "map")
({:candidate "map", :type :function}
 {:candidate "map-entry?", :type :function}
 {:candidate "map-indexed", :type :function}
 {:candidate "map?", :type :function}
 {:candidate "mapcat", :type :function}
 {:candidate "mapv", :type :function})

;; ns completion
(completions "incomplete.co")
({:candidate "incomplete.core", :type :namespace}
 {:candidate "incomplete.core-test", :type :namespace})

;; keyword completion
(completions ":v")
({:candidate ":val", :type :keyword}
 {:candidate ":valf", :type :keyword}
 {:candidate ":valid", :type :keyword}
 {:candidate ":validator", :type :keyword}
 {:candidate ":value", :type :keyword}
 {:candidate ":var", :type :keyword}
 {:candidate ":var-form", :type :keyword}
 {:candidate ":var-name", :type :keyword}
 {:candidate ":var-params", :type :keyword}
 {:candidate ":var-query", :type :keyword}
 {:candidate ":varargs", :type :keyword}
 {:candidate ":vector", :type :keyword}
 {:candidate ":vector-long", :type :keyword}
 {:candidate ":verbose", :type :keyword}
 {:candidate ":verbose?", :type :keyword}
 {:candidate ":version-string", :type :keyword}
 {:candidate ":versions", :type :keyword}
 {:candidate ":via", :type :keyword}
 {:candidate ":volatile", :type :keyword}
 {:candidate ":volatile-mutable", :type :keyword})

;; static method completion
(completions "Integer/re")
({:candidate "Integer/remainderUnsigned", :type :static-method}
 {:candidate "Integer/reverse", :type :static-method}
 {:candidate "Integer/reverseBytes", :type :static-method})

;; instance method completion
(completions ".to")
({:candidate ".toBinaryString", :type :method}
 {:candidate ".toChars", :type :method}
 {:candidate ".toCodePoint", :type :method}
 {:candidate ".toDegrees", :type :method}
 {:candidate ".toHexString", :type :method}
 {:candidate ".toIntExact", :type :method}
 {:candidate ".toLowerCase", :type :method}
 {:candidate ".toOctalString", :type :method}
 {:candidate ".toRadians", :type :method}
 {:candidate ".toString", :type :method}
 {:candidate ".toTitleCase", :type :method}
 {:candidate ".toUnsignedInt", :type :method}
 {:candidate ".toUnsignedLong", :type :method}
 {:candidate ".toUnsignedString", :type :method}
 {:candidate ".toUpperCase", :type :method})
```

By default the function operates on the current ns (`*ns*`), but you
can also specify an explicit namespace.

``` clojure
(completions "ma" 'clojure.core)
```

While `incomplete` was extracted from nREPL, it won't be used to replace nREPL's
own completion code (`nrepl.util.completion`), as nREPL can't have any runtime dependencies. The two codebases
will be synchronized manually, as we've done in the past for nREPL's [bencode library](https://github.com/nrepl/bencode). This adds a bit of maintenance overhead, but it's a small price to pay compared to the benefits.

That's all I have for you today. A short article for a simple library. I hope that some of you
are going to find `incomplete` useful!

[^1]: It's also related to me thinking of naming it `ncomplete` originally.
