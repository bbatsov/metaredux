---
layout: post
title: The Clojure Style Guide Redux
date: 2019-01-13 12:49 +0200
category: posts
tags:
- Clojure
- Style Guide
---

## Prelude

Recently I've noticed that the [Community Clojure Style
Guide](https://github.com/bbatsov/clojure-style-guide) has turned 6!
According to `git log` the project started its life on the 3rd of
January, 2013!

```
commit 3abbcfb8b536999d0b1d64b531a9c57b49b4562a
Author: Bozhidar Batsov <bozhidar@batsov.com>
Date:   Thu Jan 3 10:09:53 2013 -0800

    Initial commit
```

Time sure flies fast!

After the initial announcement of the guide, I never really revisited
the topic, so it seems to me that this "birthday", is as good of an
opportunity as any, to do so.

<!--more-->

## Impact

The main purpose of a style guide is always to educate (beginners) and
align (experts). It showcases in a concise form the convention of a
language and how to effectively use it. Its existence also spares you
from the burden of having to make a lot of trivial decisions
yourselves and makes it easier for people to work on many different
projects (provided those projects applied the same style principles).

Did the Clojure style guide achieve its primary objectives?
From my perspective it seems that the guide had a good impact on the community -
I've often seen it cited here and there, which means that people are using it.
I can only guess how many people are actually abiding by it, but I'm feeling
optimistic in this regard.

The impact was not as big, however, as I had originally envisioned,
because I never got to the second step I had planned - namely writing
a lint-like tool that enforces the guide automatically.  I pretty much
wanted to replicate what I had done for Ruby with the [Community Ruby
Style Guide](https://github.com/rubocop-hq/ruby-style-guide) and
[RuboCop](https://github.com/rubocop-hq/rubocop). I recall I spent a
lot of time thinking about the name of the lint tool, but I never
actually found time to start it.[^1]

Despite my failure, others managed to fill some of the gap - I know
that [zprint](https://github.com/kkinnear/zprint) has a `community`
formatting mode and I think that
[cljfmt](https://github.com/weavejester/cljfmt) has defaults that are
similar to what's outlined in the style guide.

## Current Status

The work on the guide has stagnated a bit in the recent years, as I've
been very busy with work and other projects, and there haven't been
many contributors to the project. When the guide was announced a lot of
people helped with growing and polishing it, but after a while the work
grinded almost to a halt.

I'd really love for us to find a few more editors to help with the
guide (and more occasional contributors, of course), so that the guide
can be completed and always kept up-to-date with the best practices in
the Clojure community. Having more editors should also dispel any
notion that despite its name (community guide), the guide reflects my
personal stylistic preferences, as opposed to those of the broader Clojure
community.

## Future Goals

So, I guess now you're wondering how exactly can you help, right? Let
me give you a few ideas - some goals I set out to achieve and never
got to accomplishing.

### Cohesion

One of the problems of the guide today is that certain sections are
just stubs, compared to others. All sections should have equally good
rationales, examples and so on. Currently that's certainly no the case
for sections like `State Management`, `Macros`, `Documentation`, etc.

A good style guide is a **cohesive** style guide. It's a style guide with
good style.

### Coverage of Newer Clojure Features

Clojure introduced many new features after the guide was created that
never made their way to it. Some things that come to mind are
transducers, specs, reader conditionals.

We should totally fix this!

### Better Rationales

I'm a big fan of Stuart Sierra's series on [Clojure Do's and
Don't](https://stuartsierra.com/tag/dos-and-donts). They are similar
to a style guide, but the articles there go into great detail when
it comes to the rationale for the suggestions in them.

While a style guide has to be concise by definition, it also has to be
clear. The Clojure style guide can certainly take a page from Stu's book and
improve the rationales of many of its suggestions.

I also think that he has covered many of topics that are not covered
in the style guide presently, so it'd be nice if we borrowed those as
well.

So, what's "better rationale" to begin with. Well, unfortunately
that's really subjective, but let me try to illustrate this with a few
examples. Here's a rule with a weak rationale (or poor examples):

>  Leverage `comp` when doing so yields simpler code.

```Clojure
;; Assuming `(:require [clojure.string :as str])`...

;; good
(map #(str/capitalize (str/trim %)) ["top " " test "])

;; better
(map (comp str/capitalize str/trim) ["top " " test "])
```

Is this really simpler? Why not use threading?

And here are a few examples a consider better - either because they are self-explanatory, or they have
better rationale attached to them:

>  Prefer `condp` instead of `cond` when the predicate & expression don't  change.

```Clojure
;; good
(cond
  (= x 10) :ten
  (= x 20) :twenty
  (= x 30) :thirty
  :else :dunno)

;; much better
(condp = x
  10 :ten
  20 :twenty
  30 :thirty
  :dunno)
```

> Prefer `case` instead of `cond` or `condp` when test expressions are compile-time constants.

```Clojure
;; good
(cond
  (= x 10) :ten
  (= x 20) :twenty
  (= x 30) :forty
  :else :dunno)

;; better
(condp = x
  10 :ten
  20 :twenty
  30 :forty
  :dunno)

;; best
(case x
  10 :ten
  20 :twenty
  30 :forty
  :dunno)
```

> Don’t use the interop syntax to
> construct type and record instances.  `deftype` and `defrecord`
> automatically create constructor functions. Use those instead of
> the interop syntax, as they make it clear that you're dealing with a
> `deftype` or a `defrecord`. See [this
> article](https://stuartsierra.com/2015/05/17/clojure-record-constructors)
> for more details.

``` Clojure
(defrecord Foo [a b])
(deftype Bar [a b])

;; good
(->Foo 1 2)
(map->Foo {:b 4 :a 3})
(->Bar 1 2)

;; bad
(Foo. 1 2)
(Bar. 1 2)
```

> Note that `deftype` doesn't define the `map->Type` constructor. It's available only for records.

I hope you get the point.

## Looking Forward

Once the goals I've mentioned above have been dealt with, I'm
considering to submit the style guide to <https://clojure.org>.
Hopefully Alex, Rich and Stu would consider it a valuable addition to
the official documentation.

I'd also love to find the time to write that lint tool I mentioned earlier, but
I doubts that's going to happen in the foreseeable future.

## Epilogue

If you've got any generic feedback about the style guide (structure,
content, examples, whatever) - I'd love to hear it!  I'm looking
forward to working with all of you on making the guide better and more
useful to all the members of the Clojure community!

Keep hacking and keep editing!

[^1]: I think my front-runners for the name were `Caliber` and `Zealot`.
