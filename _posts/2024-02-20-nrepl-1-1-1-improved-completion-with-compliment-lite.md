---
layout: post
title: 'nREPL 1.1.1: Improved Completion with compliment-lite'
date: 2024-02-20 11:18 +0200
tags:
- nREPL
- Clojure
---

Today I've released [nREPL 1.1.1](https://github.com/nrepl/nrepl/releases/tag/v1.1.1) with a couple of small bug-fixes and one more notable, if mostly invisible change.

Historically nREPL's `completions` op used internally a modified version of
`clojure-complete`, that I eventually released as a library named
[incomplete](https://github.com/nrepl/incomplete). `clojure-complete` was pretty
much abadonware at this point and I wanted a simple drop-in replacement that we
could use in tools like REPLy (and by association - Leiningen).[^1]

Using the much better
[compliment](https://github.com/alexander-yakushev/compliment) library wasn't an
option, as we needed something that was quite literally a single file. Recently,
however, `compliment`'s author Alex Yakushev released exactly what we needed - a
single-file, stripped-down version of `compliment` called
[compliment-lite](https://github.com/alexander-yakushev/compliment/tree/master/lite).

Naturally, it made sense for nREPL to adopt it and offer improved out-of-the-box
completion experience to Clojure programmers. As this doesn't affect the public
API of nREPL[^2] I've opted to ship this change in a "bug-fix" release. A version like
1.1.1 is just too cool to pass up!

In light of the creation of `compliment-lite` I now consider `incomplete` to be
obsolete and I'd encourage everyone looking for a simple, but capable code-completion
library to go for `compliment-lite` instead.

That's all I have for you today. Keep hacking!

[^1]: See <https://metaredux.com/posts/2021/08/17/introducing-incomplete-a-simple-clojure-code-completion-library.html> for more details.
[^2]: See <https://github.com/nrepl/nrepl/pull/304>.
