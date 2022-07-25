---
layout: post
title: Leiningen Adds Support for nREPL's Unix Sockets
date: 2022-07-25 11:36 +0300
tags:
- Clojure
- nREPL
---

The next release of Leiningen (likely 2.9.9) will include support for starting
an nREPL server listening to an Unix domain socket.[^1] As a reminder - Unix
domain sockets were one of the highlights of [nREPL 0.9]({% post_url
2021-12-12-nrepl-0-9 %}), which was released at the end of 2021.

The support for Unix sockets in Leiningen is two-fold. First, you'll be able to do the following:

    $ lein repl :headless :socket path/to/socket

This will start a headless nREPL, bound to the socket file you've specified. Alternatively, you'll be able to specify a `:socket` option in your `project.clj` under `:repl-options`:

``` clojure
;; Options to change the way the REPL behaves.
:repl-options {  ;; Customize the socket the repl task listens on and
                 ;; attaches to.  Specify either a filesystem :socket
                 ;; (where the parent directory should be used to
                 ;; control access since POSIX doesn't require
                 ;; respecting the socket permissions):
                 :socket "/path/to/the/socket"
                 ;; or a network :host and/or :port
                 ;;   :host "0.0.0.0"
                 ;;   :port 4001
}
```

That's useful if you always want to use the same socket file.

The work done in Leiningen essentially wraps years of work, mostly carried out by [Rob Browning](https://github.com/rlbdv), to perfect the support
for Unix sockets in nREPL and CIDER. It was a long journey, but we made it! Now it's time to sit back, relax and enjoy the fruits of this great work![^2]

[^1]: See <https://github.com/technomancy/leiningen/pull/2799>.
[^2]: Oh, well - we still need to teach CIDER how to extract the socket file automatically from Leiningen's output, but that's relatively simple.
