---
layout: post
title: 'Hard CIDER: Navigating CIDER Buffers Ninja-style'
date: 2019-10-07 20:16 +0300
categories: posts
tags:
- Clojure
- Emacs
- CIDER
- Series
- Hard CIDER
---

CIDER has way too many special buffers, doesn't it? Immediately a few come to my
mind - the REPL buffer, the stacktrace buffer, the profiler buffer, the scratch
buffer, the docs buffer and the nREPL messages buffer. There are easy ways to
summon some of them (e.g. the REPL buffer with `C-c C-z`), but not all.  Usually
you'd be looking the CIDER buffers up using `C-x b` (`switch-buffer`) and
whatever completion front-end you're using.[^1] Is there a better way to handle
navigation to and from those special buffers?

Turns out there is! Enter the little known `cider-selector` command, bound by default to `C-c M-s`.
The usage of the command is extremely simple - after invoking it you need to type a
single key identifying the target buffer (e.g. `r` for the REPL) and that's it.

Here's a list of all of `cider-selectors`'s keybindings:

| Keyboard Shortcut  | Description |
| ------------- | ------------- |
| `c` | Most recently visited Clojure buffer. |
| `e` | Most recently visited Emacs Lisp buffer. |
| `r` | Current REPL buffer or most recently visited REPL buffer. |
| `m` | `*nrepl-messages*` buffer. |
| `x` | `*cider-error*` buffer. |
| `d` | `*cider-doc*` buffer. |
| `p` | `*cider-profiler*` buffer. |
| `s` | `*cider-scratch*` buffer. |
| `q` | Abort. |
| `?` | Show help. |

Any of those keys can be prefixed with a `4` to make the target buffer open in a
different window (as opposed to the current one).

One thing to keep in mind about the default keybinding `C-c M-s` is that it's available only
in buffers where `cider-mode` is enabled (e.g. Clojure source buffers) and in the CIDER REPL.
If you want to have it available everywhere it might be a good idea to add a global
binding in your Emacs config:

``` elisp
(global-set-key (kbd "C-c s") #'cider-selector)
```

One cool thing that you probably noticed is that there are also shortcuts for going back to
whatever is the most recently used Clojure or Emacs Lisp buffer.[^2] Another cool thing is that
you can easily extend the selector with new commands using `def-cider-selector-method`:

``` emacs-lisp
(def-cider-selector-method ?z
  "CIDER foo buffer."
  cider-foo-buffer)
```

I'd be really curious to hear your ideas about additional selector actions!

The `cider-selector` command will be familiar to users of SLIME (CIDER's main inspiration), as we borrowed
it from there. Perhaps we could have named it better, but you know... naming is hard!
Rest assured, despite its weird name it's pretty useful!

That's all I have for you today. This episode was brought to you by [Clojurists Together](https://www.clojuriststogether.org/).
They are awesome and so are all of you! Keep hacking!

[^1]: I'm quite fond of `ivy`.
[^2]: I hope this will motivate some of you to work on CIDER itself.
