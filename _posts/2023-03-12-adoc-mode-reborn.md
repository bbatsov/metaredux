---
layout: post
title: adoc-mode Reborn
date: 2023-03-12 09:53 +0200
tags:
- Emacs
- AsciiDoc
---

It's no secret I'm a big of fan of both Emacs and [AsciiDoc](https://asciidoc.org/).
I've been using AsciiDoc heavily for years and most of the documentation of my bigger OSS projects is written in AsciiDoc. That being said, my experience with the only AsciiDoc major mode for Emacs (`adoc-mode`) has been a mixed bag - it's been somewhat buggy and missing some nice features that I've come to appreciate from modes like
`markdown-mode` and `org-mode`. On top of that, the mode's development has been dormant for the past 7 years and the chances of something getting fixed seemed to be slim to none.

I remember that at some point I seriously entertained the idea to start a new AsciiDoc major mode from scratch (and name it `asciidoc-mode`), but at the end I decided I didn't want to inflict so much extra work on myself. The good thing about markup formats like Asciidoc is that you use them quite effectively with minimal tooling support. I know of plenty of people who write AsciiDoc in `text-mode`.

Fast-forward to the present. After some discussions within the Emacs community I ended up taking over the maintenance of the orphaned `adoc-mode`. A new canonical fork now lives [here](https://github.com/bbatsov/adoc-mode/) and MELPA packages are being built from it.
Eventually I'll submit `adoc-mode` for inclusion in the official NonGNU ELPA Emacs package repository as well.

I have so many projects on my hands (and a day job) that I don't plan to do something major with `adoc-mode`, but at least now small improvements are happening and pull requests are getting reviewed and merged. With the help of a bunch of contributors I've managed to cut the first [new release](https://github.com/bbatsov/adoc-mode/releases/tag/v0.7.0) of `adoc-mode` last week. I'll file this milestone under "great success".

The development of `adoc-mode` 0.8 is already underway and it will feature one long-awaited feature - [native syntax highlighting for code blocks](https://github.com/bbatsov/adoc-mode/pull/21).

Long term the goal is pretty clear - modernize the internals of the package and get to feature parity with `markdown-mode`. I have no idea when we'll get there, but the goal has been set. I'm hoping that other people who love Emacs and AsciiDoc will help us get there.

That's all I have for you today. Keep hacking!
