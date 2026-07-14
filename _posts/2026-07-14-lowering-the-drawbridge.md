---
layout: post
title: "Lowering the Drawbridge"
date: 2026-07-14 10:30 +0300
tags:
- Clojure
- nREPL
---

[Drawbridge 0.4][release] is out! If your reaction is "Draw-what now?", I can't
really blame you - Drawbridge is easily the most obscure project in the nREPL
stable, and it has spent most of its life in a state best described as
"technically maintained". I've set out to change that recently, and this post
is both a release announcement and the story of a 14-year-old project that
never quite lived up to its potential. Hopefully, until now.

<!--more-->

## What's Drawbridge, anyway?

In case you've never come across it: [Drawbridge][] is an HTTP transport for
nREPL, packaged as a humble Ring handler. You mount it in your web application, and suddenly you
can REPL into that application over plain HTTP(S) - no open socket, no special
network setup, just another route in your app:

```clojure
(ns my-app.repl
  (:require [drawbridge.core :refer [secure-ring-handler]]
            [ring.adapter.jetty :refer [run-jetty]]))

(defn -main [& _]
  (run-jetty (secure-ring-handler :token (System/getenv "DRAWBRIDGE_TOKEN"))
             {:port 8080 :join? false}))
```

[Chas Emerick][cemerick] created it way back in 2012, in the golden era of
Heroku-style PaaS platforms.[^1] The problem it solved was very real: those
platforms gave your application exactly one way in - HTTP on port 80/443 - and
nREPL speaks bencode over a raw socket. If you wanted a REPL into your
production app (and of course you did, this is Clojure), you needed the REPL to
ride on HTTP. Drawbridge was that ride. The name suddenly makes a lot of sense,
doesn't it? It's the thing you lower to let people into the castle.

When I took over nREPL's maintenance in 2018, Drawbridge came along as part of
the [broader ecosystem cleanup][nrepl-redux] and moved to the `nrepl` GitHub
org. It got a couple of releases back then (0.2.0 and 0.2.1), and after that...
silence. Seven years of it.

## The bridge that never got you across

Truth be told, Drawbridge never really delivered on its promise. Let me try to
explain why.

First, security. A Drawbridge endpoint is remote code execution as a service -
that's the whole point - but the project shipped with no authentication story
at all. "Bring your own middleware" was the official answer, and the number of
people who got that right on the first try was, I suspect, not large. Exposing
your production REPL on the public internet with hand-rolled auth is the kind
of thing that keeps security teams up at night, and rightfully so.

Second, the tooling never came. CIDER, Calva, and practically every other
nREPL client connect over a plain socket with a host and a port. Drawbridge
connections only worked through `lein repl :connect` and hand-written clients -
so the audience was limited to people willing to REPL without their editor.
That's a tough sell.

Third, the transport itself was long-polling: the client keeps asking the
server "anything for me yet?" over and over. It worked, but it always felt a
bit sluggish, and it could flood your server with polling requests in the
process.

Given all that, the community sensibly routed around the problem. If you had
SSH access to the box, tunneling the nREPL port (`ssh -L`) was simpler and
more secure than anything Drawbridge offered. And nREPL itself eventually grew
native TLS support, which covered another chunk of the use cases. Between those
two, "secure remote nREPL" mostly stopped meaning "HTTP".[^2]

But the original niche never went away. Plenty of environments still give you
HTTP ingress and nothing else - PaaS platforms, container services, corporate
networks where the only thing allowed through the firewall is 443. In those
places SSH tunnels and TLS sockets aren't options, and Drawbridge remains the
only game in town. It just needed to stop being a liability there.

## Dusting it off

The modernization happened in two waves. A quiet 0.3.0 earlier this year
dragged the foundations into the present - current nREPL, Ring and friends,
Clojure 1.10+, Java 17+, a real test suite and CI. The fun stuff landed now, in
0.4, and it goes straight after the three problems above - reach, trust and
speed.

- The headline feature is the *bridge* (`drawbridge.bridge`) - a small local
  process that presents a plain nREPL socket on `localhost` and relays
  everything to a remote Drawbridge endpoint. This single thing unlocks the
  entire nREPL ecosystem: CIDER, Calva, rebel-readline, anything that speaks
  bencode can now reach a Drawbridge endpoint. It even writes a `.nrepl-port`
  file, so `cider-connect` picks it up automatically.
- Security is finally built in, and it's opt-out rather than opt-in.
  `secure-ring-handler` gives you a complete endpoint with bearer-token
  authentication (constant-time comparison, naturally) in one form, and it
  flat-out refuses to create an unauthenticated endpoint unless you explicitly
  pass `:insecure true`. Exposing an open REPL should be a deliberate act, not
  an oversight.
- There's a brand-new WebSocket transport that replaces long-polling with real
  server push - output streams to your REPL the moment it's produced. The
  bridge picks it automatically for `ws(s)://` URLs, and the server pings idle
  connections so aggressive proxies (looking at you, 55-second router
  timeouts) don't sever your session mid-thought.
- The old HTTP transport is still there as the lowest-common-denominator
  fallback, and it got some love too - including a fix for a subtle
  session-affinity race that had been quietly lurking in the client since the
  beginning.
- The bridge CLI tries to be a good citizen: it reads the token from
  `DRAWBRIDGE_TOKEN` (command-line arguments leak via `ps`), validates its
  arguments, has a `--help`, and when it can't reach the remote endpoint it
  tells you *why* right in your REPL instead of silently dropping the
  connection.
- There's a `deps.edn` now, so you can run the bridge straight from a git
  coordinate without installing anything.
- A couple of ancient issues got closed along the way, including the
  "Drawbridge clobbers my Ring session" bug that had been open since 2018.
  (It had actually been fixed for a while - now there's a regression test
  proving it.)

The end-to-end experience today looks like this. On the server - the
`secure-ring-handler` example above. On your machine:

```
$ export DRAWBRIDGE_TOKEN=<token>
$ clojure -Sdeps '{:deps {nrepl/drawbridge {:mvn/version "0.4.0"}}}' \
    -M -m drawbridge.bridge --url https://my-app.example.com/repl
```

...and then `cider-connect` (or `lein repl :connect http://localhost:7888`, or
whatever you fancy). Your editor neither knows nor cares that there's HTTP in
the middle.

## Where you might actually use it

A few scenarios worth trying:

- REPL into an app on a PaaS or container platform that only exposes HTTP(S) -
  the original 2012 use case, finally with your editor along for the ride.
- Debugging a staging environment from behind a corporate firewall that only
  lets 443 through.
- Ops-style poking at a running service where provisioning SSH access would be
  a bureaucratic adventure, but adding one authenticated route is a code
  review away.
- The WebSocket transport within your own infrastructure, simply because
  server push makes for a much nicer remote REPL than polling ever did.

To be clear - if you *do* have SSH access or can expose a TLS socket, those
remain excellent options, and I'm not here to talk you out of them. Drawbridge
is for everywhere they don't reach.

## Feedback, please!

Drawbridge spent so long in hibernation that I've genuinely lost track of who
still uses it and for what. If you take 0.4 for a spin - or if you looked at it
years ago and bounced off - I'd love to hear about it on the
[issue tracker][issues]: what works, what doesn't, what's missing. I have some
ideas of my own for where to take it next (smarter session handling is high on
the list), but I'd much rather steer by real use cases than by my own guesses.

Big thanks to Chas Emerick for building Drawbridge (and nREPL!) in the first
place - reviving a well-designed project is a pleasure, even 14 years later.
And big thanks to [Clojurists Together][ct], Nubank, and all the other
organizations and people supporting my Clojure OSS work - none of this would be
happening without you.

In the REPL we trust! Keep hacking!

[^1]: The docstring of nREPL's `url-connect` still lists
    `http://your-app-name.heroku.com/repl` among its examples, to this very
    day. 2012 was a different time - deploying to Heroku was the height of
    fashion and we all thought dynos were the future.

[^2]: Fun trivia: Leiningen's REPL client (REPL-y) has been quietly bundling
    Drawbridge all these years to make `lein repl :connect http://...` work.
    Every Leiningen user has been carrying a copy of Drawbridge 0.2.1 around
    without knowing it.

[release]: https://github.com/nrepl/drawbridge/releases/tag/v0.4.0
[Drawbridge]: https://github.com/nrepl/drawbridge
[cemerick]: https://github.com/cemerick
[nrepl-redux]: {% post_url 2018-10-29-nrepl-redux %}
[issues]: https://github.com/nrepl/drawbridge/issues
[ct]: https://www.clojuriststogether.org/
