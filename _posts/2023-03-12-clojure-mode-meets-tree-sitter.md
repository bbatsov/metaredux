---
layout: post
title: clojure-mode meets tree-sitter
date: 2023-03-12 10:27 +0200
tags:
- Emacs
- Clojure
---

It's no secret that one of the biggest weaknesses of Emacs is that features like
font-locking (syntax highlighting) and indentation are (usually) implemented in
terms of regular expressions. Even though this primitive approach mostly gets
the job done, it's both slow and quite limiting in some ways.

Emacs 29 aims to change this with the introduction of a built-in support
[Tree-sitter](https://tree-sitter.github.io/tree-sitter/). Tree-sitter is a
parser generator tool and an incremental parsing library. It can build a
concrete syntax tree for a source file and efficiently update the syntax tree as
the source file is edited. Sounds great, right?

I'm guessing that over time most Emacs major modes will adopt Tree-sitter, but
that journey will be hard and painful, mostly because of backward compatibility
with Emacs releases that don't have support for Tree-sitter. This also probably
means that built-in modes that ship with Emacs will be at the forefront of the
Tree-sitter adoption as they don't have to care about backwards compatibility.

So, what does this mean for `clojure-mode`? It means that for us the best way to
support Tree-sitter will be via a new standalone mode. Enter
[clojure-ts-mode](https://github.com/clojure-emacs/clojure-ts-mode). The new
mode lives in a separate GitHub repository and shares no code with the existing
`clojure-mode`. This means it's pretty light on features right now, but it also
means it doesn't carry all the legacy `clojure-mode` has from `lisp-mode`, on
which it was originally based. Slates don't come much cleaner than this.

Danny Freeman has been driving the work on `clojure-ts-mode` and he already got
most of the basics working, so feel free to play with the mode if you want. That
being said - a ton of work remains and help from everyone is most welcome. The
[setup](https://github.com/clojure-emacs/clojure-ts-mode#installation) is a bit
complex right now, but it's very well documented.

As CIDER (and `inf-clojure`) itself relies on the existing `clojure-mode` for
things like font-locking, indentation and finding expression boundaries, it will
be a while until `clojure-ts-mode` can fully replace `clojure-mode`, but the
first steps on the long journey have been taken. Still, I expect that most
people who switch to Emacs 29 will be able to do their basic programming in the
new mode a lot faster, as it won't be an issue to have both modes installed at the same time. Once `clojure-ts-mode` matures enough we'll likely teach CIDER about it as well, so it could leverage either it or `clojure-mode`.

Finally, on some happy day in the (not so) distant future the old `clojure-mode`
will be retired and `clojure-ts-mode` will become `clojure-mode`. Right now it's
common for the modes using Tree-sitter to be named `something-ts-mode` to
distinguish them from the existing modes that don't, but I guess eventually
we'll go back to having just one Tree-sitter-powered major mode for each
programming language. As they were saying in "Highlander" - there can be only
one!

I really believe that Tree-sitter can be a game-changer for Emacs, as it will
potentially make Emacs a lot more competitive with "modern" editors and IDEs,
that have relied on custom sophisticated parsers for a while. The combination of
Tree-sitter and LSP support should narrow a lot the gap between various editors
over time and I think this will play to Emacs's advantage. Time will tell.

That's all I have for you today. Keep hacking!
