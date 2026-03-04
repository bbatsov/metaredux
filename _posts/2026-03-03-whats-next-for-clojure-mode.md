---
layout: post
title: What's Next for clojure-mode?
date: 2026-03-03 23:30 +0200
tags:
- Clojure
- Emacs
---

Good news, everyone! [clojure-mode
5.22](https://github.com/clojure-emacs/clojure-mode/releases/tag/v5.22.0) is
out with many small improvements and bug fixes!

<!--more-->

While TreeSitter is the future of Emacs major modes, the present remains a bit
more murky -- not everyone is running a modern Emacs or an Emacs built with
TreeSitter support, and many people have asked that "classic" major modes
continue to be improved and supported alongside the newer TS-powered modes (in
our case -- [clojure-ts-mode](https://github.com/clojure-emacs/clojure-ts-mode)).
Your voices have been heard! On Bulgaria's biggest national holiday (Liberation
Day), you can feel liberated from any worries about the future of
`clojure-mode`, as it keeps getting the love and attention that it deserves!
Looking at the changelog -- 5.22 is one of the biggest releases in the last few
years and I hope you'll enjoy it![^1]

Now let me walk you through some of the highlights.

## edn-mode Gets Some Love

`edn-mode` has always been the quiet sibling of `clojure-mode` -- a mode for
editing EDN files that was more of an afterthought than a first-class citizen.
That changed with 5.21 and the trend continues in 5.22. The mode now has its own
dedicated keymap with data-appropriate bindings, meaning it no longer inherits
code refactoring commands that make no sense outside of Clojure source
files. Indentation has also been corrected -- paren lists in EDN are now treated
as data (which they are), not as function calls.

Small things, sure, but they add up to a noticeably better experience when
you're editing config files, test fixtures, or any other EDN data.

## Font-locking Updated for Clojure 1.12

Font-locking has been updated to reflect Clojure 1.12's additions -- new
built-in dynamic variables and core functions are now properly highlighted.  The
optional `clojure-mode-extra-font-locking` package covers everything from 1.10
through 1.12, including bundled namespaces and `clojure.repl` forms.[^2] Some
obsolete entries (like `specify` and `specify!`) have been cleaned up as well.

On a related note, protocol method docstrings now correctly receive
`font-lock-doc-face` styling, and `letfn` binding function names get proper
`font-lock-function-name-face` treatment. These are the kind of small
inconsistencies that you barely notice until they're fixed, and then you wonder
how you ever lived without them.

## Discard Form Styling

A new `clojure-discard-face` has been added for `#_` reader discard forms. By
default it inherits from the comment face, so discarded forms visually fade into
the background -- exactly what you'd expect from code that won't be read. Of
course, you can customize the face to your liking.

## Notable Bug Fixes

A few fixes that deserve a special mention:

- **`clojure-sort-ns` no longer corrupts non-sortable forms** -- previously,
  sorting a namespace that contained `:gen-class` could mangle it. That's fixed
  now.
- **`clojure-thread-last-all` and line comments** -- the threading refactoring
  command was absorbing closing parentheses into line comments. Not anymore.
- **`clojure-update-ns` works again** -- this one had been quietly broken and is
  now restored to full functionality.
- **`clojure-add-arity` preserves arglist metadata** -- when converting from
  single-arity to multi-arity, metadata on the argument vector is no longer
  lost.

## The Road Ahead

So, what's actually next for `clojure-mode`? The short answer is: **more of the
same**. `clojure-mode` will continue to receive updates, bug fixes, and
improvements for the foreseeable future. There is no rush for anyone to switch
to `clojure-ts-mode`, and no plans to deprecate the classic mode anytime soon.

That said, if you're curious about `clojure-ts-mode`, its main advantage right
now is **performance**. TreeSitter-based font-locking and indentation are
significantly faster than the regex-based approach in `clojure-mode`. If you're
working with very large Clojure files and noticing sluggishness, it's worth
giving `clojure-ts-mode` a try. My guess is that most people won't notice a
meaningful difference in everyday editing, but your mileage may vary.

The two modes will coexist for as long as it makes sense. Use whichever one
works best for you -- they're both maintained by the same team (yours truly and
co) and they both have a bright future ahead of them. At least I hope so!

As usual - big thanks to everyone supporting my Clojure OSS work, especially the
members of [Clojurists Together](https://www.clojuriststogether.org/)! You rock!

That's all I have for you today. Keep hacking!

[^1]: I also hope I didn't break anything. :-)
[^2]: I wonder if anyone's using this package, though. For me CIDER's font-locking made it irrelevant a long time ago.
