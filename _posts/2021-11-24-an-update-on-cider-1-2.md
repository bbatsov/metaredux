---
layout: post
title: An Update on CIDER 1.2
date: 2021-11-24 10:00 +0200
tags:
- Clojure
- Emacs
- nREPL
---

When [clj-refactor 3.0]({% post_url 2021-10-26-clj-refactor-3-0 %}) was
released, I promised you that the release of CIDER 1.2 wasn't far
behind. However, an entire month has passed since then and CIDER 1.2 is still
brewing. Turned out there was more work to be done than I expected, that's why I
decided to provide a short progress update.

Several things contributed to the delay. Here's a brief rundown in no particular order:

- nREPL 0.9 was delayed a bit by some problems we've discovered with the new Unix socket transport. The problems have been addressed and I expect that we'll finalize nREPL 0.9 in the next week or so.
- Due to the nREPL issues mentioned above, we still haven't added support for the new Unix socket transport to CIDER.
- There's some cleanup work that has to be done in Orchard with respect to switching
from `dynapath` to [enrich-classpath](https://github.com/clojure-emacs/enrich-classpath) for automatically adding Java sources and Javadoc to the classpath. That cleanup is optional for this particular release, but I'd rather do it sooner rather than later.
- I want us to support properly [nbb](https://github.com/babashka/nbb) in CIDER 1.2. This requires some changes to the ClojureScript bootstrap logic in CIDER, as `nbb`
is a native Node.js implementation that works differently from all the ClojureScript REPLs we're currently supporting (notably you don't need to evaluate any code in a Clojure REPL to "upgrade" it to a ClojureScript REPL).
- I still haven't submitted CIDER and its dependencies to [NonGNU ELPA](https://elpa.nongnu.org/nongnu/), so that people would be able to install CIDER out-of-the-box on Emacs 28.
- I've been crazy busy at work, which limited the amount of time I could allocate to CIDER and friends.

In light of everything listed above, right now I'm aiming to release CIDER 1.2
around Christmas. Note that the current CIDER snapshot is in a pretty good shape
overall and you can upgrade to it without any concerns about the stability
issues.

If you want to know more about the current state of affairs, please check out
this [meta ticket](https://github.com/clojure-emacs/cider/issues/3090), which
tracks all the remaining work.  As usual any help with the outstanding tasks
will be greatly appreciated!

To wrap up on a positive note - in the mean time we released `clj-refactor` 3.1 and 3.2 with a bunch of small improvements!
We might be moving a bit too slow at times, but we're always moving forward!
