---
layout: post
title: 'Hard CIDER: REPL Shortcuts'
date: 2019-05-15 11:25 +0300
categories: posts
tags:
- Clojure
- Emacs
- CIDER
- Series
- Hard CIDER
---

One common struggle people have with CIDER (and Emacs in general) is that there are way too many keybindings to remember, and some of them require pressing a lot of keys. While there are many ways to address those common problems in Emacs, today I'll focus on one that's really simple and CIDER specific.

CIDER's REPL provides a neat trick inspired by SLIME - REPL shortcuts. They are extremely easy to use - just press `,` while at the REPL prompt and you'll get a completing read allowing you run one of the CIDER commands that are bound to REPL shortcuts. Let's now revisit [tidying up our REPL]({% post_url 2019-05-11-hard-cider-keeping-your-repl-tidy %}) in the context of REPL shortcuts you can get a better idea of how this actually works:

![CIDER REPL Shortcuts](/assets/images/hard_cider_repl_shortcuts.gif)

Note that this example uses the [ivy library](https://github.com/abo-abo/swiper) to display the list of commands that are available. You'll get different visuals if you're using some other minibuffer completion library (e.g. `ido` or `icomplete`).

If you don't like using `,` as the trigger key you can change it to some other key easily. Just put this in your Emacs configuration:

``` emacs-lisp
(setq cider-repl-shortcut-dispatch-char ?\!)
```

The above example changes the trigger key to `!`. Be careful what you set here, as it shouldn't be something with which a Clojure form might start. If you selected `'`, for instance, you'll have a really hard time entering quoted forms in your CIDER REPL.

There are many commonly used commands that are bound to REPL shortcuts out-of-the-box. You can examine them by selecting `help` after pressing `,`. The shortcuts functionality is user-extensible and you can easily add more commands there:

``` emacs-lisp
(cider-repl-add-shortcut "clear-output" #'foo-cmd)
(cider-repl-add-shortcut "clear" #'bar-cmd)
(cider-repl-add-shortcut "clear-banners" #'baz-cmd)
```

That's all I have for you today. Simple, sweet and maybe even useful! Keep hacking!
