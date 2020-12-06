---
layout: post
title: Semantic Clojure Formatting
date: 2020-12-06 12:31 +0200
tags:
- Clojure
- Code Style
---

> Programs must be written for people to read, and only incidentally for machines to execute.
>
> -- Harold Abelson, Structure and Interpretation of Computer Programs

The above maxim has guided most of my career as a software engineer and has been one of
the central [guiding principles](https://guide.clojure.style/#guiding-principles) of the [community Clojure style guide](https://guide.clojure.style),
that I've been maintaining for the past (almost) 8 years.

While the style guide was well received by the Clojure community in general, and universal agreement was reached on most points in it, one aspect
remains controversial to this day - namely the style guide's preference towards "semantic indentation" over "fixed indentation" for function/macro
arguments.
Today I wanted to address an article that suggested that fixed indentation is preferable, as it's simpler and it's easier to provide tooling support for it.
That article was named [Better Clojure Formatting](https://tonsky.me/blog/clojurefmt/), which admittedly is catchy and made me consider naming my
response to it "The Best Clojure Formatting" or "Superb Clojure Formatting", but I've opted for a more modest title in the end.

Before I go into my arguments let's briefly contrast semantic and fixed indentation with a couple of examples.

``` clojure
;;; Semantic indentation
;;
;; macros
(when something
  (something-else))

(with-out-str
  (println "Hello, ")
  (println "world!"))

;; function call spanning two lines
(filter even?
       (range 1 10))

;; function call spanning three lines
(filter
 even?
 (range 1 10))


;;; Fixed indentation
;;
;; macros
(when something
  (something-else))

(with-out-str
  (println "Hello, ")
  (println "world!"))

;; function call spanning two lines
(filter even?
  (range 1 10))

;; function call spanning three lines
(filter
  even?
  (range 1 10))
```

I hope it's obvious that the main difference is that "semantic indentation" differentiates between macros with body forms and regular function/macro invocations.
That makes it easy to visually tell the apart. The "fixed indentation" approach, however, advocates ignoring those differences and indenting everything the same way.

**NOTE:** There are other aspects of "semantic indentation" as well, but I'll stick only to the most prominent/infamous parts.

The main argument in favor of "fixed indentation" is tooling support - if you don't need to format something differently, obviously creating code formatters becomes easier. There's nothing for the formatter to parse/analyze, and there's no need for configuration. Simplicity is generally a good thing, so this definitely sounds appealing.
While, I'm all for consistency and good tooling support, I don't think that's that big of a deal in the case of Clojure, though. Consider the following:

* For many macros you can infer the right indentation just by looking for a `body` parameter

``` clojure
(defmacro when
  "Evaluates test. If logical true, evaluates body in an implicit do."
  {:added "1.0"}
  [test & body]
  (list 'if test (cons 'do body)))
```

Of course, there will also be counter-examples that need special handling, as they used some non-conventional names, but
this simple heuristic will work quite well most of the time.

* It's easy to specify the right indentation with [metadata](https://docs.cider.mx/cider/indent_spec.html)
* All popular Clojure editors and formatters that exist today are supporting "semantic indentation" pretty well, so clearly it's not a big obstacle for tooling.

In general I've always found it dangerous that a lot of the reasoning
around code style in recent years has been centered around how easy it is to enforce
certain style automatically.[^1]  I've experienced this first-hand both as
an editor of several code style guides, and as the author of a [fairly
popular Ruby lint/formatter tool](https://docs.rubocop.org).  Yeah,
lint tools and formatters are very important and they make our lives easier, but we should
never forget we're writing code for humans to read and (only occasionally) for machines to
execute. It's be really said if we lost sight of that just to pursue the ultimate degree
of consistency.

And there's also the meta topic of the code we write being some artistic expression of our inner self, but we should
probably disregard this argument in any professional context. I'll mention, though, that due to the simplicity
of Clojure/Lisp we're (un)fortunate to have way fewer stylistic decisions to make than developers working with
languages like Ruby, Java or JavaScript.

Before, I wrap up I want to elaborate a bit on the reasoning behind the (infamous) semantic rules for function arguments alignment:

``` clojure
;; function call spanning two lines
(filter even?
       (range 1 10))

;; function call spanning three lines
(filter
 even?
 (range 1 10))
```

While some people would argue that the rules are random or inconsistent, I believe that's definitely not the case:

* In the first example, the formatting is similar to the one in every Algol-like language where the name of the function stands out and the arguments are grouped together. Of course, in most Algol-like languages people are still arguing whether to align or not align their function/method arguments. That being said, the case for aligned has always been the case for improved readability.
* In the second example some people would question what they call "the 1-space indent". That's not really indentation at all, but rather standard alignment of list elements. Don't forget the `()` are not some special invocation syntax, but plain old list literals, that are normally formatted like any other collection literal:

``` clojure
;; list literal
(1
 2
 3)

;; vector literal
[1
 2
 3]

;; set literal
#{1
  2
  3}
```

I don't know about you, but I find the following fixed indentation a big weird:

``` clojure
;;; Fixed Indentation
;;
;; list literals
(1 2 3
  4 5 6)

(1
  2
  3
  4
  5
  6)
```

Admittedly, list literals are not very common in Clojure, that's why it's understandable
that for many people lists are nothing but an invocation syntax.

"Better Clojure Formatting" argued that the semantic formatting guidelines were not consistent:

> This rule would be easy to automate if it didn’t have a huge list of exceptions. `let`, `cond`, `if`, `defn` and many other forms simply don’t follow this rule.

I disagree with this assessment, as the guideline in question is strictly for forms without bodies (mostly regular function calls).
All of the cited exceptions are just regular cases of a different [rule](https://guide.clojure.style/#body-indentation).

> Again, exceptions! E.g. `try` doesn’t follow it. Also, having a mix of one- and two-space indents was driving me mad when I was trying to follow those rules manually.

Same here. Obviously `try` is not part of the guideline in question and follows a different guideline, which is hardly an exception.

## One Formatter to Rule Them All

I'll also briefly touch upon the subject of "one formatter to rule them all", that
was the main driver for "Better Clojure Formatting".

I think that adopting an universal formatter/code style 10 years into
the existence of a language is unlikely to (fully) succeed if it's not
driven from the top (Rich). There will always be strong opposition to
whatever we decide (as community), as people have built strong preferences at this
point and they'll need extremely compelling arguments to change
them. Change is hard, and no one really wants to deal with it,
especially if they don't have to.

`gofmt` succeeded mostly because it was pushed from the top, pushed
from the start and everyone was expected to use it. I'm reasonably
sure this ship has failed for Clojure, just as it has sailed for
Ruby. I don't know how successful the similar projects for other
languages are, but my guess would be they aren't much more successful
than Ruby's `RuboCop`.

I know only that `Prettier` (a JavaScript formatter) is
quite successful and very widely used, but it's also configurable to some extent and it
didn't really propose anything novel or controversial in terms of formatting.
That's why I think that the only way for a tool to gain much traction
would be if it's aiming to enforce something relatively close to what
people are doing currently.

My observations are that the semantic approach to formatting is still more popular
in the wild, and I really hope that this is never going to change.

I'll also glad that the two most popular Clojure formatters that exist today (`cljfmt` and `zprint`)
have embraced the community Clojure style guide (it's basically the default for `cljfmt` and a [configuration
flag](https://github.com/kkinnear/zprint/blob/master/doc/options/community.md) for `zprint`).

## Closing Thoughts

Anyways, I don't really want to argue which style is better or worse.[^2] My one and only point is that
I'll always value readability over some questionable gains in the tooling department.
I've also updated the Clojure style guide, as part of the [Advent of Open Source]({% post_url 2020-12-03-advent-of-open-source %}), to feature
a lot more rationale for the controversial formatting rules (start [here](https://guide.clojure.style/#one-space-indent and read on).

That's all I have for you today. As usual, I'd love to hear your thoughts on the subject. Keep hacking!

[^1]: Damn you, `gofmt`!
[^2]: For the record, I've got nothing but immense respect for Nikita and his work, even if I disagree with him on this topic.
