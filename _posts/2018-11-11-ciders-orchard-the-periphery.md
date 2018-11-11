---
layout: post
title: "CIDER's Orchard: The Periphery"
category: posts
tags:
- Clojure
- Emacs
- CIDER
- Orchard
- nREPL
---

## Prologue

This post is part of the mini-series "CIDER's Orchard". Currently
I'm planning 3 installments in the series, but this might change
depending on my inspiration.

* [CIDER's Orchard: The Heart]({% post_url 2018-11-09-ciders-orchard-the-heart %})
* CIDER's Orchard: The Periphery
* CIDER's Orchard: The Future

<!--more-->

## The Periphery of the Orchard

The Periphery of CIDER's Orchard includes many Clojure libraries that
are the underpinnings of important, but non-critical CIDER features.
My definition of "non-critical" is "features that are nice to
have, but you can be reasonably productive even without them".

Unlike the libraries that live in the Heart of the Orchard, most
libraries in the Periphery were not created, as part of CIDER's
development.[^1] There are quite a few libraries living in the Periphery
of the Orchard, so brace yourselves for a rather long post.

[^1]: `refactor-nrepl`, `profile` and `sayid` being the exceptions.

### cljfmt

[cljfmt][] powers the `cider-format-*` family of commands. Those
commands are pretty handy if people are part of teams where not
everyone's using Emacs/CIDER and using an external formatting tool is
the only way to get everyone's code formatted in the same manner.

#### Project Status

The project is widely used in the Clojure community and it's actively
maintained and developed.

There's just one thing that's really needed to take the support for it
in CIDER to the next level - [teaching cljfmt about indentation
metadata](https://github.com/weavejester/cljfmt/issues/49).
If someone would like to tackle this ticket, that'd be so amazing!
That would also be a major step forward towards adopting a [common
indentation specification](http://www.cider.mx/en/latest/indent_spec/)
across the Clojure ecosystem!

### profile

[profile](https://github.com/thunknyc/profile) powers CIDER's profiling functionality.

Unfortunately, it seems we never really documented the built-in profiling support, so I wonder
how many people even know that it exists.[^2]
Hopefully this post will inspire some of you to check it out! The
profile commands are available under the `C-c C-=` prefix (and under
`Profile` in `cider-mode`'s menu).

[^2]: What a wonderful idea for a new blog post!

#### Project Status

The project has been dormant for 4 years, so I guess it could use some
help. On the other hand - it has 0 outstanding issues, so it can just
as easily be perfect.[^3]

[^3]: It could just as easily mean that no one is using CIDER's profiler.

### java.classpath

We currently use [java.classpath][] to power CIDER's [classpath
browser](https://cider.readthedocs.io/en/latest/miscellaneous_features/#classpath-browser)
and all sorts of classpath introspection functionality (e.g. checking
if some required libraries are present on the classpath and so on).

The `orchard` library actually provides a Boot-aware wrapper around
`java.classpath`, that understands all the classpath magic that
happens around Boot's classpath handling.
These days CIDER doesn't use `java.classpath` directly - only via `orchard`.

#### Project Status

`java.classpath` is a Clojure Contrib project.
It hasn't seen much changes over the past years, but given
its simplicity - it doesn't really need much maintenance either. The only big
change that happened was related to the release of Java 9.

`java.classpath` is a simple library and ideally we should just get replicate its
functionality in `orchard`, so we can make `orchard` self-contained.

### tools.namespace

CIDER uses [tools.namespace][] in two ways:

1. To power the `cider-ns-refresh` code-reloading functionality.
2. As a generic ns-search API (e.g. to find all project namespaces).

The [reloaded
workflow](http://thinkrelevance.com/blog/2013/06/04/clojure-workflow-reloaded)
generated a lot of buzz a few years back, so I assume a lot of CIDER
users make use of `cider-ns-refresh`.

#### Project Status

`tools.namespace` is a Clojure Contrib project.  It seems to be
unmaintained. The work on the 0.3 branch started some years ago and
stopped at 0.3.0-alpha4. `tools.namespace` gets the job done, but then
again there are certainly problems with it that needed to be addressed
at some point.

That's a super popular and important library in the Clojure ecosystem, so the current
situation is definitely unfortunate.

I think that a good course of action would be to replicate at least
the ns-search functionality in Orchard directly, and to potentially
tackle the ns-reloading as well at some point. Generally the less the
dependencies we have to Clojure Contrib projects, the better.

### tools.reader

CIDER uses [tools.reader][] in its `cider-format-edn` family of commands. Not sure
how many CIDER users make use of those, so it's safe to say `tools.reader` is not
particularly important part of CIDER's Orchard.

#### Project Status

`tools.reader` is a Clojure Contrib project.
It is under active development and has [an awesome
maintainer](https://github.com/Bronsa). Unfortunately, it also has a
[bus factor of
1](https://github.com/clojure/tools.reader/graphs/contributors).[^4]

[^4]: Which is actually the best bus factor you can get in Clojure Contrib these days.

### tools.trace

CIDER uses [tools.trace][] to power its [built-in execution tracing
functionality](https://cider.readthedocs.io/en/latest/miscellaneous_features/#tracing-function-execution).

#### Project Status

`tools.trace` is a Clojure Contrib project.
Like most Clojure Contrib projects, there's not a lot going on in `tools.trace`, but
it gets the job done and that's what matters the most at the end of the day.
I don't recall a single tracing-related issue to have ever been reported to CIDER,
so I guess we're good here.

### sayid

[sayid][] is a turbo-charged tracing library[^5]. It ships with an nREPL middleware
and a very powerful Emacs UI (basically, an extension for CIDER).

While it's not currently part of CIDER itself, I'm hoping that one day
`sayid` is going to be promoted to a first-grade CIDER component, as
it's way more powerful than `tools.trace`.

[^5]: Amongst many other things.

#### Project Status

The project is part of CIDER's organization, but it doesn't have a
maintainer right now and we're actively looking for one (or more). I
apply the occasional bugfix, but that's the extent of the love `sayid`
gets today, and it certainly deserves way more attention.

### clj-refactor/refactor-nrepl

[clj-refactor][] and [refactor-nrepl][] are the biggest projects
living in the Orchard's Periphery.  They've got some extra
dependencies themselves, but I'll focus only on the top-level
projects for now.

`clj-refactor` and `refactor-nrepl` extend CIDER with a lot of
commands for all sorts of common refactoring tasks (e.g. renaming
vars, extracting bindings/functions, cleaning up `ns` forms and so
on). There's some really good stuff there! Just like `cider-nrepl`,
`refactor-nrepl` was created for CIDER, but it's not tied to CIDER and
other editors are leveraging the nREPL middlewares provided by it.

Some of you are probably wondering why this functionality is not part
of CIDER and `cider-nrepl`, right?

The answer to this question is two-fold:

* Even now I can't keep up with the current core CIDER codebase and
I'm very wary when it comes to having to maintain more complex
code. I've already reached the limit of what I can handle.[^6]
* `clj-refactor.el` and `refactor-nrepl` make use of some dependencies
  that add a lot of complexity - e.g. `clj-refactor.el` uses heavily
  packages `multiple-cursors` and `refactor-nrepl` powers some of its
  functionality by doing static code analysis with
  `tools.jvm.analyzer`. As building the AST for a project takes some time,
  and requires to evaluate all of the project's namespaces in the process,
  there's a lot of inherent complexity that goes with it.

Generally I believe that we should be working towards gradually
merging the libraries into CIDER, but we should be very careful with
the complexity overhead. There's plenty of functionality in
`refactor-nrepl` that doesn't rely on project-wide static code
analysis and I believe we should start there.

By the way, don't get me wrong - there's nothing wrong the static code
analysis approach.  It's just not something we have the bandwidth to
support given the current state of CIDER and its ecosystem.

Probably if the parsing was done without evaluating the code I would
have been more enthusiastic about this. It seems to me that a
simplistic parser that ignores macro expansions would yield 90% of the
benefits with 10% of the complexity.

Perhaps one of you, my dear Clojurians, would like to take a stab at
something like this?

[^6]: I think I actually reached this limit at least a couple of years ago.

#### Project Status

We've historically struggled to keep `cider-nrepl` and
`refactor-nrepl` in sync, but lately the APIs have stabilized and the
situation has been a bit better. Generally the main problem has always
been the fact that the two projects were developed by different groups
of people and `refactor-nrepl` would often trail a bit upstream
changes in CIDER.

The problems were recently compounded by the fact that the maintainers
of `clj-refactor` and `refactor-nrepl` have had very little time for them and we barely
managed to get new releases out that were compatible with CIDER 0.18.

These awesome projects really need some help and some love!

### Interlude - The Emacs Part of the Orchard

There are actually way more projects related to CIDER -
[clojure-mode](https://github.com/clojure-emacs/clojure-mode),
[sesman](https://github.com/vspinu/sesman) (CIDER's session manager)
and a myriad of Emacs extensions (some of which are documented
[here](http://www.cider.mx/en/latest/additional_packages/)).

It's not like they don't need help and work - quite the contrary!
I've opted, however, to focus on the Clojure's side of CIDER's
ecosystem, as improvements to it are going to result in improvements
across the board (the greater Clojure ecosystem).

### Epilogue

Time to wrap up this installment, as it's getting pretty long already. If
I had to summarize the post to its very essence it'd be something
like:

* The Orchard is Huge!
* Some Clojure Contrib projects that we use are liabilities right now and ideally we should replace them with other libraries.
* Key projects like `sayid` and `refactor-nrepl` are dangerously close to being abandonware and we really more contributors there!

If it seems to you that maintaining CIDER's Orchard is an extremely
time-consuming and stressful work, you're totally right.  Often I
wonder how we managed to get so far with the limited resources that we
have and the never-ending problems that impede big advancements in
many directions.

The current situation is not sustainable and these series should serve
as a wake up call for everyone who didn't understand it.  Can we turn
things around? Can we ensure the future of CIDER's Orchard? Can we build
a strong foundation for Clojure development tools?

That's going to be the topic of the next post in the series.

Until then - keep hacking!

[cljfmt]: https://github.com/weavejester/cljfmt
[java.classpath]: https://github.com/clojure/java.classpath
[tools.namespace]: https://github.com/clojure/tools.namespace
[tools.reader]: https://github.com/clojure/tools.reader
[tools.trace]: https://github.com/clojure/tools.trace
[sayid]: https://github.com/clojure-emacs/sayid
[clj-refactor]: https://github.com/clojure-emacs/clj-refactor.el
[refactor-nrepl]: https://github.com/clojure-emacs/refactor-nrepl
