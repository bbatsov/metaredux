---
layout: post
title: clj-refactor.el 3.0
date: 2021-10-26 09:06 +0300
tags:
- Clojure
- Emacs
- nREPL
---

I'm happy to report, that after an almost entire year of development `clj-refactor.el` 3.0 is finally
out! Without a doubt that's the most significant release the project has had in years, even if you probably
won't be able to tell this by reading the modest [release notes](https://github.com/clojure-emacs/clj-refactor.el/releases/tag/3.0.0).
Somewhat ironically, we also failed to achieve the main objective we had for this release - namely [restoring the dependency hot-loading functionality](https://github.com/clojure-emacs/clj-refactor.el/issues/466), which has been broken ever since JDK 9 was released and the new module system wreaked havoc in our
old implementation of the functionality. So, why the major release then?

Well, those of you who monitor the project closely know that it wasn't getting a lot of love in the past several years.
Even though I'm one of the maintainers of the project, I never really used it much and I wasn't particularly invested in it.
I just had way too much work with nREPL, CIDER and Orchard to be able to dedicate a lot of time to another relatively big project.
Still, I'd be reviewing PRs, cutting new releases occasionally and making sure that `clj-refactor.el` mostly works.
This year, however, there was a surge of contributions to the project that finally pushed it forward in significant ways.
Most of those contributions, however, happened in the underlying nREPL middleware - namely `refactor-nrepl`.

`refactor-nrepl` 3.0 is also out and you'll notice that the [changes there](https://github.com/clojure-emacs/refactor-nrepl/releases/tag/v3.0.0)
are significantly more and much deeper. It's safe to say that's the biggest release since version 2.3, which was released somewhere in 2017 if memory
serves. The codebase is now much cleaner, leaner and faster. The test suite got a lot of improvements. While there's no new functionality
in this release, a lot of things are significant better than they used to be and that's a big deal.

Note that the 3.0 version number doesn't really imply any breaking API changes
at the nREPL level this time around. It mostly reflects the massive internal
changes to the codebase (e.g. the code dealing with namespace cleanup was
completely reorgnized and slimmed down significantly). We are well aware of the
fact that `refactor-nrepl` is used by many nREPL-powered programming
environments today and we're very careful when it comes to breaking changes
there. I'm hoping that most users will experience a very smooth upgrade the
latest `refactor-nrepl` release.

I won't be going over any of the technicalities of what was improved in `clj-refactor.el` and `refactor-nrepl`, as the release notes for both
releases are quite detailed. I'll just mention that I'm very excited that the projects are finally gaining some traction and I hope that it will
last. I find these two releases to be a great example of the power of open-source - anyone can push a project forward if he has the will and the energy to do so.
These particular releases were largely carried out by [vemv](https://github.com/vemv), who has been helping a lot recently with all sorts of CIDER-related projects.
Once again `vemv` receives a big "Thank you!" from me!

So, what does the future has in store for the both projects? Well, we still have to bring back hot-loading, but there's no shortage of
ideas for what we can improve. Probably down the road we'll decouple `clj-refactor.el` releases from `refactor-nrepl` releases, as it was
done in CIDER a couple of years ago. More importantly, though, we'd like to show that we haven't reached the limit of what one can do with
a purely (mostly?) REPL-powered approach to refactoring. We see that with the rise of `clojure-lsp` and tools like `clj-kondo` some people
are starting to question the tradional Lisp ways, but we remain convinced that they are just as powerful and as relevant as they have always been.
And as simple and elegant as Lisp. That's all from me for now. In the REPL we trust!
