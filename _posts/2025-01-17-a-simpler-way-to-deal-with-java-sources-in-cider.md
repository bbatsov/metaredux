---
layout: post
title: A Simpler Way to Deal with Java Sources in CIDER
date: 2025-01-17 13:02 +0200
tags:
- CIDER
- Clojure
- Emacs
- Java
---

For ages dealing with Java sources in CIDER has been quite painful.[^1] Admittedly,
much of the problems were related to an early design decision I made to look for
the Java sources only in the classpath, as I assumed that would be easiest way
to implement this. Boy, was I wrong! Countless of iterations and refinements to
the original solution later, working with Java sources is still not easy.
[enrich-classpath](https://github.com/clojure-emacs/enrich-classpath) made
things better, but it required changes to the way CIDER (nREPL) was being started
and slowed down the first CIDER run in each project quite a bit, as it fetches
all missing sources at startup. It's also a bit trickier to use it with
`cider-connect`, as you need to start nREPL together with `enrich-classpath`.
Fortunately, my good friend and legendary Clojure hacker [Oleksandr
Yakushev](https://github.com/alexander-yakushev) recently proposed a [different
way](https://github.com/clojure-emacs/cider/pull/3769) of doing things and today
I'm happy to announce that this new approach is a reality!

There's an exciting new feature waiting for you in the latest CIDER MELPA build. After
updating, try turning on the new variable `cider-download-java-sources`
(`M-x customize-variable cider-download-java-sources` and then toggle to enable
it). Now CIDER will download the Java sources of third-party libraries for Java
classes when:

* you request documentation for a class or a method (`C-c C-d C-d`)
* jump to its definition (`M-.`)

This feature works without `enrich-classpath`.[^2]  The
auto-downloading works for both tools.deps and Leiningen-based projects. In both
cases, it starts a subprocess of either `clojure` or `lein` binary (this is the same
approach that Clojure's 1.12 `add-lib` utilizes).

And that's it! The new approach is so seamless that it feels a bit like magic.

This approach should work well for most cases, but it's not perfect. You
might have problems downloading the sources of dependencies that are not public
(i.e. they live in a private repo), and the credentials are non-global but under
some specific alias/profile that you start REPL with. If this happens to you,
please report it; but we suspect such cases would be rare.  The download usually
takes up to a few seconds, and then the downloaded artifact will be reused by
all projects. If a download failed (most often, because the library didn't publish
the `-sources.jar` artifact to Maven), CIDER will not attempt to download it again
until the REPL restarts.  Try it out in any project by jumping to
`clojure.lang.RT/toArray` or bringing up the docs for
`clojure.lang.PersistentArrayMap`.

Our plan right now is to have this new feature be disabled by default in CIDER
1.17 (the next stable release), so we can gather some user feedback before
enabling it by default in CIDER 1.18.  We'd really appreciate your help in
testing and polishing the new functionality and we're looking forward to hear if
it's working well for you!

We also hope that other Clojure editors that use `cider-nrepl` internally (think Calva,
`iced-vim`, etc) will enable the new functionality soon as well.

That's all I have for you today! Keep hacking!

**P.S.** The [State of CIDER 2024 survey](https://forms.gle/H24Z1Yk79mGAnAHn8) is still open and it'd be great if you took a moment to fill it in!

[^1]: You need to have them around to be able to navigate to (definitions in) them and improved Java completion. More details [here](https://github.com/clojure-emacs/orchard?tab=readme-ov-file#dealing-with-java-sources).
[^2]: If you liked using `enrich-classpath` you can still continue using it going forward.
