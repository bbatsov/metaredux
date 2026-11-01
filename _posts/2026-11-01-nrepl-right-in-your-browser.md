---
layout: post
title: "nREPL, Right in Your Browser"
date: 2026-11-01 11:06 +0200
tags:
- Clojure
- nREPL
- nrepl-sci
---

Last week [nrepl.org](https://nrepl.org) got a makeover. The docs moved to
[docs.nrepl.org](https://docs.nrepl.org), and nrepl.org itself became a
landing page that explains what nREPL is and how its protocol works, with
"wire windows" showing real exchanges between a client and a server, as EDN or
as the bencode that actually goes over the wire. Reading about a protocol only
gets you so far, though, so the page now also has a ["Try it"
section](https://nrepl.org/#try) with a real nREPL server running right in
your browser. There's one in this post as well! In the rest of it I'll tell
you how it came about, why it was harder than it looks, and how you can use
the server behind it for your own projects.

<!--more-->

Here it is:

{% include nrepl-sci-demo.html %}

Go ahead, evaluate something! Tick "show the messages" to see the requests
and the responses going back and forth, and try `(range)` if you're curious
what happens to code that never finishes.

## Picking an approach

An interactive nREPL playground always runs into the same problem: you need
an nREPL server somewhere, and a server that evaluates code from random people
on the internet is just about the worst thing you can host. (It would also
cost money that I'd rather spend on something else.) So the server had to run
in the browser, and I looked at a few ways to get there.

The first one was the real thing. [CheerpJ](https://cheerpj.com) is a JVM
that runs in the browser, and nREPL 1.8 and Clojure 1.12 worked on it
remarkably well. Every op I tried worked, `stdin` included! The
problem was everything around that - about 8 seconds to a ready server on a
fast machine, about 4.5MB to download, a busy loop that freezes the whole page
unless you put the JVM in a Web Worker, and an `interrupt` that works only on
code that's sleeping.[^1] That's a lot to ask of someone who just opened a
landing page.

The second one was [scittle](https://github.com/babashka/scittle), which runs
[SCI](https://github.com/babashka/sci) (Michiel Borkent's Clojure interpreter)
in the browser and comes with an nREPL server,
[sci.nrepl](https://github.com/babashka/sci.nrepl). It solves a different
problem, though - it lets your editor evaluate code in a page, through a relay
that runs in babashka or on the JVM, and its replies differ from nREPL's in
quite a few places. (I've filed [some
issues](https://github.com/babashka/sci.nrepl/issues) about those.)
[nbb](https://github.com/babashka/nbb) has a pretty complete nREPL server of
its own, but it runs only on Node.

None of those fit, so I ended up writing a small nREPL server for SCI that
runs entirely in a web page, with the page as its client -
[nrepl-sci](https://github.com/nrepl/nrepl-sci). SCI does all the heavy
lifting, and evaluation, completion and docs lookup took just a few hundred
lines of ClojureScript. The whole thing is about 230KB gzipped and it's ready
in a fraction of a second. Not bad, right?

## The hard part

A basic nREPL server is easy enough. The trouble starts with two ops most
people never think about - `interrupt` and `stdin`.

JavaScript gives you one thread, and SCI evaluates code in one go. While
`(loop [] (recur))` runs nothing else does, so the server can't even read the
`interrupt` request that would stop it, and code calling `read-line` can't
wait for input without blocking the very thread that would receive it. There's
a third problem hiding in there as well. nREPL sessions are supposed to be
independent, so an eval in one session (say, your editor's) shouldn't hold up
another one (say, a test runner connected to the same server).

Here's how nrepl-sci deals with all of that:

- The server runs in two Web Workers. A supervisor takes the requests, queues
  them, and answers `interrupt` and `stdin` itself, while an evaluator runs
  SCI.
- The two share some memory (a `SharedArrayBuffer`) that serves as a
  mailbox. The evaluator waits for mail with `Atomics.wait`, which blocks a
  worker without spinning, so `read-line` waits for its input there, much like
  it would on the JVM.
- SCI calls a hook of your choice on every function call and every `recur`.
  nrepl-sci uses it to check its mailbox, to see if it's been interrupted, or
  if there's a request from another session it could take in between. Checking
  on every call made code noticeably slower, so it checks on every 256th call,
  which still means every few microseconds. Benchmarks run about as fast as
  without any of this.
- Endless seqs like `(range)` and `(iterate inc 0)` check as they go, so
  `(count (range))` stops when you interrupt it.
- Some code never calls back into SCI - a regex that backtracks forever, for
  instance. The supervisor gives it a second to stop, then kills the evaluator
  and starts a new one. Your sessions survive that, but everything you
  evaluated in them is gone.

In the end the supervisor, the evaluator and the mailbox took more code than
the nREPL server itself, which tells you something about where the
complexity is.

**Note:** All of this works only when the page is [cross-origin
isolated](https://web.dev/articles/cross-origin-isolation-guide), as browsers
hand out `SharedArrayBuffer` only to pages served with the
`Cross-Origin-Opener-Policy` and `Cross-Origin-Embedder-Policy` headers.

Sadly, GitHub Pages, which hosts both nrepl.org and this blog, can't send
those. Without them nrepl-sci falls back to doing one thing at a time:
`read-line` throws, and interrupting code means starting over, which is exactly
what you saw if you tried `(range)` above. Evaluation, output, errors,
completion and `doc` work the same either way, though.

## Testing with proof

Back in 2023 I listed the lack of a protocol compatibility test suite among
the [things people hate about nREPL]({% post_url 2023-03-26-bad-nrepl %}),
and asked if one of you was looking for a fun small OSS project. Eventually I
took my own advice and started [proof](https://github.com/nrepl/proof), and
nrepl-sci is the first server I've written with proof around from day one. It
made a huge difference! Building an nREPL server used to involve a lot of
guessing, as the protocol is small, but what clients actually expect from a
server is spread across their source code.

A proof profile is a short TOML file that tells proof how to start your server
and how to say a few things in its language: a value, some output, an
exception, reading a line, code that runs until it's stopped. `proof run`
takes it from there, with checks covering the protocol and the exact requests
CIDER, Calva, Conjure, vim-fireplace, REPLy and neorepl send. When a check
fails, proof also tells you why it matters:

```
FAIL   robust.sessions-at-once      An eval in one session doesn't hold up the others
       clone request got no reply with "done" within 10s
       why: Each client has sessions of its own (e.g. an editor and neorepl,
       connected to the same server), so a server that runs one eval at a time
       leaves every other client waiting for whatever the code in one of them
       does.
```

That's a check you can easily shrug off when you're writing a server for a
playground, and it's the reason nrepl-sci takes requests in between at all.
Working on the server mostly came down to running `make proof` after every
change. Today nrepl-sci passes all 73 checks that apply to it (the other 5 are
about things like vim-fireplace, which needs Java). Without shared memory it
fails exactly two: `stdin.need-input` and `robust.sessions-at-once`. I guess
that's a pretty good summary of what you give up without cross-origin
isolation.

If you're working on an nREPL server of your own, I can't recommend proof
enough. It also comes with a GitHub Action, so your CI can run it as well.

## The tradeoffs

Nothing comes for free, and nrepl-sci is no exception:

- It's SCI, not Clojure. You're getting ClojureScript as interpreted by SCI,
  so there are no Java classes, no JVM libraries, and some corners of the
  language behave differently. That's fine for a playground, but not for
  anything that needs your real project.
- There's no JavaScript interop. SCI blocks it unless you allow it, and
  nrepl-sci doesn't, so `(js/alert "hi")` and even `(.toUpperCase "hi")` fail.
  Admittedly, that's a bit annoying, but it's also what makes nrepl-sci safe
  to embed anywhere, as the code people type can't touch the page, or even the
  worker it runs in. (`clojure.string/upper-case` works just fine.)
- A request from another session runs on top of the code it interrupted, so
  that code can't stop until the request is done, and a `read-line` taken in
  between holds it until it gets its input. You'd need two clients fighting
  over the same server to notice.
- Starting over loses your definitions, which matters most without
  cross-origin isolation, where every interrupt starts over.
- It's about 1MB of JavaScript (230KB gzipped), loaded the first time you use
  the REPL. That's nothing compared to CheerpJ, but it's not free either.

## Using nrepl-sci yourself

nrepl-sci was built for nrepl.org, but nothing in it is specific to the site.

You can embed a REPL in a page - documentation, a tutorial, a blog post. The
worker takes the bencode of each request as a `Uint8Array` and posts back the
bencode of each reply, so the whole integration boils down to something like
this:

```js
const worker = new Worker("/assets/js/nrepl-sci-worker-0.1.0.js");
worker.onmessage = (event) => console.log(decode(event.data));
worker.postMessage(new TextEncoder().encode(encode({ op: "eval", code: "(+ 1 2)", id: "1" })));
```

`encode` and `decode` are a few dozen lines of bencode, and the REPL in this
post is about 170 lines in total, styles included - [have a
look](https://github.com/bbatsov/metaredux/blob/master/_includes/nrepl-sci-demo.html).
Grab `nrepl-sci-worker.js` from the [latest
release](https://github.com/nrepl/nrepl-sci/releases/latest) and serve it from
your own site, as browsers start workers only from the page's own origin. If
you can send the cross-origin isolation headers, you get `stdin` and proper
interrupts for free. nrepl.org's
[playground](https://github.com/nrepl/nrepl.org/blob/master/assets/js/playground.js)
is a bigger example, with completion, history and the wire windows.

You can also test nREPL clients without a JVM. The Node build is a regular
nREPL server on a socket (`node nrepl-sci.js --port 7888`) that's up in about
a tenth of a second, which makes it handy for the test suites of clients and
editor plugins.

Finally, if you're after an nREPL server for some other language that runs on
a single thread, the supervisor and the mailbox might give you a few ideas.
The server itself knows nothing about threads or sockets, and the trick with
the mailbox works for any interpreter that can call back now and then while
it runs.

## What's next

The obvious next step is getting cross-origin isolation on nrepl.org. A
service worker can add the headers GitHub Pages won't send, at the cost of a
reload when it's first installed, so I might try that on a page of its own.
I'd also like to allow some harmless interop, so that `(.toUpperCase "hi")`
works as people expect, and to publish nrepl-sci on npm.

## Epilogue

Back in 2018 I wrote that there was ["the big dream to port nREPL to
ClojureScript"]({% post_url 2018-10-29-nrepl-redux %}), so it could be used in
self-hosted environments, and in 2023 I was still hoping we'd see more native
ClojureScript nREPL servers. nrepl-sci isn't quite that dream - it's a much
smaller nREPL server for SCI - but it's a step in that direction.

Big thanks to Michiel Borkent for SCI, which did most of the hard work here.
None of this would exist without it! And, as usual, special thanks to
[Clojurists Together](https://www.clojuriststogether.org/) for supporting my
work on nREPL!

That's all I have for you today. Play with the REPL, try to break it, and let
me know how it went! Keep hacking!

[^1]: CheerpJ's free tier also covers only non-commercial open-source projects that load it from Leaning Technologies' CDN. That would have been fine for nrepl.org, but it's one more string attached.
