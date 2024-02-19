---
layout: post
title: 'CIDER: Preliminary Support for clojure-ts-mode'
date: 2024-02-19 09:57 +0200
tags:
- CIDER
- Clojure
- Emacs
---

I'm glad to report that yesterday the long-awaited preliminary support for `clojure-ts-mode` in CIDER [has landed](https://github.com/clojure-emacs/cider/pull/3622)!

This pull request (or rather the original PR on which it was based) was in the works for a very long time and it feels good to see it finally merged.
What does this mean in practice? Well, CIDER will now properly recognize `clojure-ts-mode` and modes derived from it, meaning most of the functionality in CIDER
will work reasonably well with them. There are a few caveats to keep in mind, though:

- CIDER still has a hard dependency on `clojure-mode`, as it relies on some APIs from it that have yet to be ported to `clojure-ts-mode`
- Some functionality like dynamic indentation and dynamic font-locking (syntax highlighting) is still not support by `clojure-ts-mode`
- You need to use the latest version of `clojure-ts-mode`, as it features some related changes
- The new code hasn't been test much, so probably we'll encounter some bugs along the way
- You need to be on Emacs 29 to be able to use `clojure-ts-mode` in the first place

At any rate - we've made one big step towards decoupling CIDER from `clojure-mode` and gradually we'll get there. Thanks to everyone who was involved in
making this happen! Keep hacking!
