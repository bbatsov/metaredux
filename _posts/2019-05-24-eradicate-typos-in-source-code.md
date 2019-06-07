---
layout: post
title: Eradicate Typos in Source Code
date: 2019-05-24 16:28 +0200
category: posts
tags:
- Meta
- Programming
- Spell Checking
---

I don't know about you, but I keep making typos like crazy[^1], so I'm really
glad that today we've got so many tools to help us with their discovery and
mitigation.  While most text editors have some spell checking for the comments
in source files[^2], that generally is not enough to free our source files from
typos - often we'd make typos in identifiers, some team mates won't be using
spell checkers at all, some people would use British spellings instead of US
spellings and so on.

<!--more-->

Recently I found a really nice tool called
[misspell](https://github.com/client9/misspell) that really helps with solving
such problems. It's written in Go and is super fast. The basic usage is fairly
simple[^3]:

```
cider git:(master) $ misspell .
cider-debug.el:724:22: "untill" is a misspelling of "until"
cider-macroexpansion.el:47:55: "refered" is a misspelling of "referred"
cider-macroexpansion.el:48:32: "refered" is a misspelling of "referred"
CHANGELOG.md:927:9: "ouput" is a misspelling of "output"
cider-resolve.el:98:18: "referal" is a misspelling of "referral"
cider-resolve.el:99:33: "referal" is a misspelling of "referral"
cider-repl-history.el:414:32: "highlighed" is a misspelling of "highlighted"
cider-repl-history.el:421:33: "highlighed" is a misspelling of "highlighted"
cider-repl-history.el:429:36: "highlighed" is a misspelling of "highlighted"
cider-repl-history.el:434:32: "highlighed" is a misspelling of "highlighted"
cider-repl-history.el:574:3: "udpated" is a misspelling of "updated"
cider-repl-history.el:644:51: "highlighed" is a misspelling of "highlighted"
cider-stacktrace.el:656:27: "occuring" is a misspelling of "occurring"
nrepl-client.el:178:3: "Auxillary" is a misspelling of "Auxiliary"
```

It's not easy to see this, but `misspell` found typos in both comments and
source code identifiers. By default `misspell` allows both British and US
spellings, but you can be more specific about your preference:

```
cider git:(master) $ misspell -locale US *.el
cider-cheatsheet.el:341:60: "cancelled" is a misspelling of "canceled"
cider-completion.el:179:52: "recognised" is a misspelling of "recognized"
cider-completion.el:181:36: "recognised" is a misspelling of "recognized"
cider-debug.el:47:20: "behaviour" is a misspelling of "behavior"
cider-inspector.el:42:20: "behaviour" is a misspelling of "behavior"
cider-eval.el:80:27: "behaviour" is a misspelling of "behavior"
cider-util.el:659:52: "cheque" is a misspelling of "check"
nrepl-client.el:427:15: "behaviour" is a misspelling of "behavior"
nrepl-client.el:567:46: "synchronise" is a misspelling of "synchronize"
```

I'm one of those people who learned English with British textbooks and for me
that's a mega useful feature, as I constantly opt for the British spellings,
even though I know in Computer Science we're not supposed to use them.

`misspell` can also fix the typos it finds, which is a huge time saver:

```
cider git:(master) $ misspell -w -locale US doc
doc/configuration/eldoc.md:25:0: corrected "behaviour" to "behavior"
doc/configuration/eldoc.md:33:4: corrected "behaviour" to "behavior"
doc/configuration/eldoc.md:35:36: corrected "Behaviour" to "Behavior"
doc/configuration/basic_configuration.md:85:0: corrected "behaviour" to "behavior"
doc/faq.md:49:38: corrected "favourite" to "favorite"
doc/clojurescript.md:183:32: corrected "favour" to "favor"
doc/clojurescript.md:221:4: corrected "favourite" to "favorite"
doc/repl/configuration.md:6:59: corrected "behaviour" to "behavior"
doc/repl/configuration.md:90:22: corrected "behaviour" to "behavior"
doc/repl/configuration.md:137:57: corrected "behaviour" to "behavior"
doc/repl/configuration.md:153:9: corrected "behaviour" to "behavior"
doc/repl/configuration.md:156:33: corrected "behaviour" to "behavior"
doc/repl/configuration.md:156:69: corrected "behaviour" to "behavior"
doc/repl/configuration.md:182:35: corrected "behaviour" to "behavior"
doc/repl/configuration.md:239:48: corrected "behaviour" to "behavior"
```

Like most such tools it's not super reliable (meaning you'll get some false
positives here and there), but it has been very useful to me overall. If you're
an extra diligent person you can easily add it to your CI build and ensure that
all typos are properly accounted for. The project's
[FAQ](https://github.com/client9/misspell#faq) is full of useful tips to get the
most of `misspell`, so I encourage you to check it out.

That's all I have for you today. Now let's eradicate some nasty typos!

[^1]: Especially in blog posts.

[^2]: <https://emacsredux.com/blog/2019/05/24/spell-checking-comments/>

[^3]: The examples are using this project <https://github.com/clojure-emacs/cider>.
