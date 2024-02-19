---
layout: post
title: Configuring fixed/tonsky indentation in clojure-mode
date: 2024-02-19 11:45 +0200
tags:
- Emacs
- Clojure
---

A few years ago Nikita Tonsky made popular a [certain style of formating Clojure
code](https://tonsky.me/blog/clojurefmt/), that became known as "fixed" or
"tonsky" indentation in the community.

`clojure-mode` has long had some support for this via `clojure-indent-style`:

``` emacs-lisp
(setq clojure-indent-style 'always-indent)
```

However, it was kind of hard to get exactly the same indentation from Nikita's
article, as there was no way to suppress the usage of indent specs and forms
starting with a keyword were indented by separate rules from what was set by
`clojure-indent-style`. A recent [upstream
change](https://github.com/clojure-emacs/clojure-mode/commit/e201ed69d73118cad9c5a8f76ba5972df3c3a990)
made the indentation configuration more granular and now you can get fixed
indentation with the following snippet:

``` emacs-lisp
(setq clojure-indent-style 'always-indent
      clojure-indent-keyword-style 'always-indent
      clojure-enable-indent-specs nil)
```

`clojure-indent-keyword-style` and `clojure-enable-indent-specs` are the new additions, that made this possible. Here's how
`clojure-indent-keyword-style` can be used:

* `always-align` (default) -  All
    args are vertically aligned with the first arg in case (A),
    and vertically aligned with the function name in case (B).

``` clojure
(:require [foo.bar]
          [bar.baz])
(:require
 [foo.bar]
 [bar.baz])
```

* `always-indent` - All args are indented like a macro body.

``` clojure
(:require [foo.bar]
   [bar.baz])
(:x
   location
   0)
```


* `align-arguments` - Case (A) is indented like `always-align`, and
    case (B) is indented like a macro body.

``` clojure
(:require [foo.bar]
          [bar.baz])
(:x
   location
   0)"
```

By the way, `clojure-ts-mode` also supports the fixed indentation style:

``` emacs-lisp
(setq clojure-ts-indent-style 'fixed)
```

The configuration here is a lot simpler, as this functionality existed since day 1.

For the record, I still don't endorse/like fixed indentation[^1] (and funny
enough - neither does Arne, who implemented those changes). Still, I acknowledge
that this style is somewhat popular in the Clojure community and I'm all for
making it easier for people who like/need it to be able to use it.

That's all I have for you today. Keep hacking!

[^1]: See <https://metaredux.com/posts/2020/12/06/semantic-clojure-formatting.html>
