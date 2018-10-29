---
layout: post
title: "nREPL's Secret TTY Transport"
date: 2018-10-21 18:10:56 +0200
categories: posts
tags:
- Clojure
- nREPL
---

Ever since I took over the development of
[nREPL](https://github.com/nrepl/nrepl) a few months ago, I've been
spending a lot of time learning about its inner workings and
[documenting them meticulously](https://nrepl.xyz).[^1] I've learned a
ton so far, and I'd like to share some of my findings.  I hope this post
is going to be the first of a series of posts on nREPL internals, but I
won't make any promises. So, let's get to the point!

<!--more-->

Most nREPL users probably know that it normally uses a socket-based
communication protocol where the request/response messages are
[bencoded](https://wiki.theory.org/index.php/BitTorrentSpecification#Bencoding).[^2]
While that's true, it's not the whole picture - nREPL actually bundles
a second transport which implements a pretty classic streaming socket
REPL (very similar to the socket REPL added to Clojure itself in
version 1.8). Why doesn't anyone know of this alternative built-in
transport? Well, I guess one reason is that this functionality was
only mentioned in passing in the project's README, and the other is
that probably few people needed something like this.[^3]

Generally the best use-case for this transport is when you want to
communicate with nREPL via some "dumb" TTY client like `telnet`, `nc`
or maybe Emacs's
[inf-clojure](https://github.com/clojure-emacs/inf-clojure). I think this might be
particularly useful when you simply want to check something quickly and don't have your
favourite editor/IDEs handy.

Using the TTY transport is pretty simple. You just need to start an
nREPL server with TTY transport and you're good to go:

```clojure
(require
 '[nrepl.server :as server]
 '[nrepl.transport :as transport])

(server/start-server :port 12345 :transport-fn transport/tty :greeting-fn transport/tty-greeting)
```

Please, note that the `:greeting-fn` is responsible for printing the
initial message you'll see upon connecting. It's not really required,
but without it you'll not see any greeting message and it might not be
immediately apparent to you that the connection has succeeded.

Afterwards you can simply connect to the server with some TTY client
like `telnet`, `nc` or `inf-clojure`. This example uses `nc` as
`telnet` was removed from recent version of macOS.

```
$ nc localhost 12345

;; Clojure 1.9.0
user=>
```

If you're into `inf-clojure` there you can simply do `M-x inf-clojure-connect` and start
hacking away in your nREPL-powered TTY REPL!

Unfortunately right now there's no way to specify the transport
which an nREPL server should use with the built-in
command-line interface, but that's probably going to change in
upcoming nREPL versions.[^4]

I've got absolutely no idea how useful this information is to any of
you, but I find it pretty cool.  This should give you an idea about
how flexible nREPL's architecture is and how much possibilities this
opens up for nREPL users - e.g. EDN wasn't a thing when nREPL was
created, but creating a transport that uses EDN instead of bencode is
a rather simple endeavour. Same with something like JSON, etc. Not to mention
you're not tied to socket-based communication at all!

I guess the most interesting (and _practical_) manifestation of the
flexible transports idea is
[drawbridge](https://github.com/nrepl/drawbridge), which adds http
support to nREPL.

[^1]: I plan to write a lot more down the road about the process of migrating nREPL out of Clojure Contrib and creating its new site/manual.
[^2]: nREPL calls this communication abstraction [transport](https://nrepl.xyz/nrepl/0.4.5/design/transports.html).
[^3]: The bencode transport is much more powerful and pretty-much all nREPL-powered tools use it for a good reason.
[^4]: Especially if we implement a built-in EDN transport as well.
