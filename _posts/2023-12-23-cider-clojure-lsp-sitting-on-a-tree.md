---
layout: post
title: CIDER & clojure-lsp Sitting on a Tree
date: 2023-12-23 9:32 +0200
tags:
- Clojure
- CIDER
---

> Less is more unless more is more.
>
> -- Anonymous

CIDER (and nREPL by association) and `clojure-lsp` are often seen as competitors when it comes to Clojure development tools. You can find plenty of discussions online
discussing the merits of both tools and their approaches.[^1]

There are two common problems that I've noticed:

1. People tend to conflate the specific `clojure-lsp` implementation and it's approach based on static code analysis with LSP in general. In the end they attribute to LSP some attributes that are not related to the protocol. E.g. you can totally implement a Clojure LSP server in terms of code evaluation (perhaps by using nREPL internally).
2. People tend to believe it's a choice of one or the other tool, while in reality they work reasonably well in combination.

Let's start by examining quickly CIDER's and `clojure-lsp`'s strengths and limitations.

## The Case for clojure-lsp

**Disclaimer:** I never really used `clojure-lsp`, so I might be missing something.

- It doesn't require you to have a running a REPL
- It operates on your entire codebase, not just the parts that are currently
  loaded (which makes it shine in areas like "find references" and refactorings
  across multiple source files)
- If you're already using `lsp-mode` or `eglot` you don't have to learn anything new to work with Clojure

Out of those benefits - only the last one is something we can attribute to the use of LSP, and I guess that the static analysis part of the `clojure-lsp` codebase is quite usable even without LSP.

## The Case against clojure-lsp

- You'll (likely) still need a REPL for optimal results with Clojure
- The LSP protocol is generic, which is great for building clients implementing it, but imposes some limitations with the functionality it can provide out-of-the-box

Basically, it seems to me that to leverage the full power of Clojure you'll still need some REPL-powered tool, alongside `clojure-lsp`.

## The Case for CIDER

- Powered by a the classic REPL-driven Lisp approach
- The information it obtains from the Clojure runtime is guaranteed to be accurate (although it may be stale)
- All of CIDER's functionality is optimized for Clojure, as it's not restrained by the LSP protocol

In a nutshell, CIDER is an embodiment of the concept of [interactive programming](https://docs.cider.mx/cider/usage/interactive_programming.html) (a.k.a. REPL-driven programming). I grew fascinated by it when I started to use Emacs and subsequently SLIME for Common Lisp programming, and those tools are the direct inspiration for CIDER.

Now that I'm quite used to this style of programming for me it feels like the
most natural thing in the world. I'll admit, though, that coming to it from Java
and IntelliJ IDEA definitely wasn't easy and I was originally quite confused by
many of the concepts that were new to me.

## The Case against CIDER

- It's (currently) coupled with nREPL, which might be a problem for some people. (e.g. those who prefer a different REPL server)
- Powered by a the classic REPL-driven Lisp approach (yeah, that's both a feature and a problem depending on your perspective) - by this I mean mostly that you can run into issues like loaded code being out-of-sync with your sources files and that operations like "find usages across a project" can't be implemented reliably.
- Working on multiple projects introduces extra complexity, as you need some session/connection management.
- Steep learning curve.

## Making them work together

In general I think that `clojure-lsp` is the clear winner until you decide to
start a Clojure REPL. If you're the type of person who likes to work without a
REPL you'll definitely appreciate `clojure-lsp`. Perhaps you can even get away
without using something like CIDER at all, but at least for me - the big feature
of Lisp is interactive programming, so I can't imagine anyone programming
without a REPL.

Once a REPL is up and running things become a lot more dicey. Why so? Because
both tools provide some similar functionality (e.g. completions, eldoc, find
references, etc) and you'll have to spend some time on the configuration to
decide what to use.

CIDER could have suppressed all the `lsp-mode`/`eglot` provided features when
active and they could have done the same, but that's not the spirit of Emacs,
right?[^2]

That's why I believe it should be up to the end users to decide which features
they want to leverage from both tools. `lsp-mode` already provides a lot of
[documentation on the
topic](https://emacs-lsp.github.io/lsp-mode/tutorials/clojure-guide/), and I
guess it'd be nice if CIDER had more coverage as well. That's definitely
something I plan to work on, but contributions are always welcome.

## REPL-driven vs Static Code Analysis

While the article mostly focuses on CIDER and `clojure-lsp` it could have easily
been about REPL-driven and static analysis powered Clojure tooling instead. CIDER has
long had an extension called `clj-refactor` that makes heavy use of static
analysis. `clj-kondo` is another great tool powered by static analysis, that
combines well with CIDER. (and so it happens that `clojure-lsp` is using internally
`clj-kondo`'s code analyzer)

While people often speak about such tools as competitors, in practice they are
often complementary and you'd better results if you combine some of them together.

## Epilogue

I think that in the end of the day what's the right tool for someone really
depends on their preferences and their style of programming. I've been
programming in Lisps for almost 20 years and I grew super accustomed to the
REPL-driven style of programming, so I don't mind its limitations much.[^3] That
being said I can definitely see the benefit of using something like
`clojure-lsp` alongside CIDER, and I'll keep
working towards making CIDER play better with `clojure-lsp`.

Keep hacking!

[^1]: E.g. [this one](https://www.reddit.com/r/Clojure/comments/d01ux4/is_lspclojure_redundant_with_cider/)
[^2]: See <https://batsov.com/articles/2021/11/16/why-emacs-redux/>.
[^3]: Although early on I definitely missed some of the refactoring capabilities of Eclise and IntelliJ IDEA.
