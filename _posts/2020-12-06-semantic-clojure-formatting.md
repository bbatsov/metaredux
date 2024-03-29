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
arguments. Today I want to address an article that suggested that fixed indentation is preferable, as it's simpler and it's easier to provide tooling support for it.
That article is named [Better Clojure Formatting](https://tonsky.me/blog/clojurefmt/)[^1], which admittedly is catchy and made me consider naming my
response to it "The Best Clojure Formatting" or "Superb Clojure Formatting", but I've opted for a more modest title in the end.

<!--more-->

## Prologue: The Purpose of Formatting

Have you ever thought why formatting (layout) is important at all?
Not just in code, but in any text in general.[^2]

The answer to this question is quite simple - proper formatting
can enhance a lot the readability of any text. And here's also the crux
of it - computers don't really care about readability. As long as some
code is syntactically correct any compiler or interpreter would be happy
to compile or run it.

For us humans, however, formatting can be very helpful or extremely confusing.
As far as I'm concerned any formatting that makes some code easier to read and
comprehend is good formatting.

## Introducing Semantic Formatting

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

;; function call spanning two lines (wide formatting)
(filter even?
        (range 1 10))

;; function call spanning three lines (narrow formatting)
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
That makes it easy to visually tell them apart. The "fixed indentation" approach, however, advocates ignoring those differences and indenting everything the same way.

A different way to think about semantic indentation might be to compare it to syntax highlighting - normally different constructs in a programming language (e.g. special forms, built-in functions and user-defined functions), get highlighted
differently, to reflect their semantics and to assist the reader in comprehending what they are dealing with.

**NOTE:** There are other aspects of "semantic indentation" as well, but I'll stick only to the most prominent/infamous parts in this article.

## Semantic Formatting vs Fixed Formatting

The main argument for "semantic formatting" is pretty simple - it
reflects better the semantics of your code, which results in improved
clarity/readability. Just by looking at the shape of the code you can
infer how something is supposed to behave.

A secondary argument might be that's also the formatting that has been employed by
all major Lisp dialects (e.g. Common Lisp, Scheme, Emacs Lisp) for a very long time.
Some might argue that following in their footsteps is just [cargo culting](https://en.wikipedia.org/wiki/Cargo_cult_programming),
but if the premise behind their approach is still relevant today I don't think the cargo cult theory has any merit.

Clojure developers have historically had a love/hate relationship with Lisp tradition, depending on their background.
That's normal, given that Clojure was often marketed as a modern Lisp that break away from some arcane Lisp dogmas and traditions
and aligns itself with modern programming practices.
Tradition, however, doesn't always mean bad, obsolete and irrelevant. In the case of semantic indentation, I think Clojure's
predecessors had the right idea and nothing has fundamentally changed.

The main argument in favor of "fixed indentation" is tooling support - if you don't need to format something differently, obviously creating code formatters becomes easier. There's nothing for the formatter to parse/analyze, and there's no need for configuration. Simplicity is generally a good thing, so this definitely sounds appealing.
While, I'm all for consistency and good tooling support, I think those are also achievable with semantic indentation in the case of Clojure (although a bit harder). Consider the following:

* For many macros you can infer the right indentation just by looking for a `body` parameter:

``` clojure
(defmacro when
  "Evaluates test. If logical true, evaluates body in an implicit do."
  {:added "1.0"}
  [test & body]
  (list 'if test (cons 'do body)))
```

Of course, there will also be counter-examples that need special handling, as they used some non-conventional names, but
this simple heuristic will work quite well most of the time.

**Note:** Common Lisp's SLIME [has been using this approach](https://common-lisp.net/project/slime/doc/html/Semantic-indentation.html) for a very long time.

* It's easy to specify the right indentation with [metadata](https://docs.cider.mx/cider/indent_spec.html)
* All popular Clojure editors and formatters that exist today are supporting "semantic indentation" pretty well, so clearly it's not a big obstacle for tooling.

In general I've always found it dangerous that a lot of the reasoning
around code style in recent years has been centered around how easy it is to enforce
certain style automatically.[^3]  I've experienced this first-hand both as
an editor of several code style guides, and as the author of a [fairly
popular Ruby lint/formatter tool](https://docs.rubocop.org).  Yeah,
lint tools and formatters are very important and they make our lives easier, but we should
never forget we're writing code for humans to read and (only occasionally) for machines to
execute. It'd be really sad if we lost sight of that just to pursue the ultimate degree
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

``` ruby
# Ruby (aligned arguments)
some_method(foo, bar
            baz)

# Ruby (indented & aligned arguments)
some_method(
  foo, bar
  baz
)
```

The above example is in Ruby, but it could have easily been in Java, C#, Python, JavaScript, etc.

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
that for many people lists are nothing but an invocation syntax.[^4]

"Better Clojure Formatting" argued that the semantic formatting guidelines were not consistent:

> This rule would be easy to automate if it didn’t have a huge list of exceptions. `let`, `cond`, `if`, `defn` and many other forms simply don’t follow this rule.

I disagree with this assessment, as the guideline in question is strictly for forms without bodies (mostly regular function calls).
All of the cited exceptions are just regular cases of a different [rule](https://guide.clojure.style/#body-indentation).

> Again, exceptions! E.g. `try` doesn’t follow it. Also, having a mix of one- and two-space indents was driving me mad when I was trying to follow those rules manually.

Same here. Obviously `try` is not part of the guideline in question and follows a different guideline, which is hardly an exception.

## Wide vs Narrow Formatting

I've noticed that many people dislike semantic formatting, because
it's too wide (in the sense of taking up horizontal space) for their
taste. A reader remarked he finds this wide formatting ugly, wasteful
of horizontal space, and prohibitive of descriptive (long) function
names:

``` clojure
;; semantic (wide)
(clojure.core/filter even?
                     (range 1 10))
```

It's important to understand that the comparison of wide vs narrow formatting has nothing to do with
semantic vs fixed. I think the confusion comes mostly from the fact that the semantic formatting is
often used in its wide version, although there's a matching narrow version for everything.

``` clojure
;; semantic (narrow)
(clojure.core/filter
 even?
 (range 1 10))

(cond
  (zero? n) curr
  :else (recur nxt (+' curr nxt) (dec n)))

;; semantic (wide)
(cond (zero? n) curr
      :else (recur nxt (+' curr nxt) (dec n)))
```

Fixed formatting, on the other hand, is narrow-only by definition.

``` clojure
;; fixed (narrow-only)
(clojure.core/filter
  even?
  (range 1 10))

(cond
  (zero? n) curr
  :else (recur nxt (+' curr nxt) (dec n)))

(cond (zero? n) curr
  :else (recur nxt (+' curr nxt) (dec n)))
```

I hope this illustrates the point I'm trying to make clearly. As long as you
understand that there's a big difference between reflecting the code
semantics in its indentation and indenting something widely/narrowly,
I've achieved my goal with this section.

## One Formatter to Rule Them All

I'll also briefly touch upon the subject of "one formatter to rule them all", that
was the main driver for "Better Clojure Formatting".

I find the idea of a universally accepted formatter with no
configuration options to be appealing, but I doubt that's feasible in
practice. When I started to work on RuboCop many years ago, it wasn't
configurable and many people were outraged by this. The Ruby community
was 15 years old at the time (today Clojure is 12 years old), many
code formatting patterns existed and very few people cared about
global code consistency - most cared about getting consistency in
their projects (and in some cases - their organizations), and of
course - with their own style preferences. Luckily for us Clojure's
syntax and semantics are much simpler than Ruby's. Making RuboCop configurable
was instrumental to its wide adoption in the Ruby community - we never
got complete alignment in the style department, but we got **some**
alignment and this definitely beats having **none**. Some newer Ruby
formatters are trying to rekindle the idea of the one true code formatter,
but I've lost interest in such endeavors myself. I'm sharing my Ruby
experience on the subject mostly because I think a lot of parallels
can be made with the state of affairs in the Clojure community today.

I think that adopting an universal formatter/code style over 10 years into
the existence of a language is unlikely to (fully) succeed if it's not
driven from the top (Rich). There will always be strong opposition to
whatever we decide (as community), as people have built strong preferences at this
point and they'll need extremely compelling arguments to change
them. Change is hard, and no one really wants to deal with it,
especially if they don't have to.

`gofmt` succeeded mostly because it was pushed from the top, pushed
from the start and everyone was expected to use it. I'm reasonably
sure this ship has sailed for Clojure, just as it has sailed for
Ruby. I don't know how successful the similar projects for other
languages are, but my guess would be they aren't much more successful
than Ruby's `RuboCop`.

I know that `Prettier` (a JavaScript formatter) is quite successful
and very widely used, but it's also configurable to some extent and it
didn't really propose anything novel or controversial in terms of
formatting.  That's why I think that the only way for a code
formatting tool to gain much traction in a community with some
established traditions would be if it's aiming to enforce something
relatively close to what people are doing currently.

Nikita made one interesting observation when he made the case for the
"one true formatter":

> This is why config/hard-coded rules/exception-based solutions don’t work here.
> When Clojure 1.6 added `when-some` and `if-some`, no formatters supported it and as a result, `when-let` and `when-some` was formatting differently.
> This is because `when-let` was is config but `when-some` wasn’t. This also means library macros never would be formatted correctly.
>
> The important thing to understand here is that such changes DID happen in the past and WILL happen in the future.
> So we can’t rely on “let’s enumerate all the exceptions and live forever with them”.

Technically speaking, Nikita is right, but I really don't think the problem is particularly serious.

First of all, I'll use the opportunity for a shameless self-plug - the indentation metadata approach solves this problem, without
requiring settling for inferior formatting. Sadly, it's still not very common and no one knows if it will ever be.
Still, I think most formatters can tackle this with the arglists heuristic I mentioned earlier. Here's the definition of `when-some`:

``` clojure
(defmacro when-some
  "bindings => binding-form test

   When test is not nil, evaluates body with binding-form bound to the
   value of test"
  {:added "1.6"}
  [bindings & body]
  (assert-args
     (vector? bindings) "a vector for its binding"
     (= 2 (count bindings)) "exactly 2 forms in binding vector")
   (let [form (bindings 0) tst (bindings 1)]
    `(let [temp# ~tst]
       (if (nil? temp#)
         nil
         (let [~form temp#]
           ~@body)))))
```

Clearly, its parameters `bindings` and `body` are named in the canonical idiomatic way, and it's easy to infer how to format this macro.

Those considerations aside, here we're discussing a problem that's common in many languages and is typically solved either by:

* *(A process solution)* The developers of the formatters in question adding support for some new language/library features before they are released (that's what I do with Ruby and RuboCop).
* *(A technical solution)* A bit of configuration to fill in the blanks. I know that's not ideal, but such changes happen infrequently and are pretty straightforward to handle.

My observations are that the semantic approach to formatting is still more popular
in the wild, and I really hope that this is never going to change.

I'll also glad that the two most popular Clojure formatters that exist today (`cljfmt` and `zprint`)
have embraced the community Clojure style guide (it's basically the default for `cljfmt` and a [configuration
flag](https://github.com/kkinnear/zprint/blob/master/doc/options/community.md) for `zprint`).

## Epilogue

> Nearly everybody is convinced that every style but their own is
> ugly and unreadable. Leave out the "but their own" and they're
> probably right...
>
> -- Jerry Coffin (on indentation)

As noted in the beginning of the article the purpose of formatting
should be to enhance the clarity and the readability of the code. Unfortunately,
in the various programming communities the endless debates on what
constitutes the optimal code layout have became synonymous with
[bikeshedding](https://exceptionnotfound.net/bikeshedding-the-daily-software-anti-pattern/).
Some people are so frustrated with this state of affairs, that they'd
rather opt for any formatting that can be applied consistently, rather
than for formatting that would complement best the semantics of the
underlying code. I totally get their reasoning, but it saddens me
regardless.

Anyways, I don't really want to argue which style is better or worse. My one and only point is that
I'll always value readability over some questionable gains in the tooling department. I can only hope
that my perspective on the matter resonated with some of you.

I've also updated the Clojure style guide, as part of the [Advent of Open Source]({% post_url 2020-12-03-advent-of-open-source %}), to feature
a lot more rationale for the controversial formatting rules (start [here](https://guide.clojure.style/#body-indentation) and read on).

That's all I have for you today. As usual, I'd love to hear your thoughts on the subject. Keep hacking!

[^1]: For the record, I've got nothing but immense respect for Nikita and his work, even if I disagree with him on this topic.
[^2]: Can you imagine reading a book where there are no paragraphs? Or a book where the text is not justified?
[^3]: Damn you, `gofmt`!
[^4]: It was brought to my attention that this examples were incorrect, as the fixed indentation was supposed to be applied only for lists that start with a symbol.
