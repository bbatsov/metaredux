---
layout: post
title: A Cornucopia of nREPL Updates
categories: posts
tags:
- Clojure
- nREPL
---

When I wrote about [nREPL 0.7]({% post_url 2020-03-28-nrepl-0-7 %}) last week I mentioned that some really exciting things were happening in
the broader nREPL/Clojure community and today I want to expand a bit on those. The general theme today is that nREPL
is getting more mindshare and broader tool support. The subtheme is that many tools can end up being
able to support more programming languages due to nREPL's [language-agnostic nature]({% post_url 2019-01-12-nrepl-beyond-clojure %}).

The format I've adopted for this post is a bit chaotic and unstructured, but I hope you'll forgive me. So, here we go.

## iced-vim Adds support for nREPL's Sideloader

We didn't have to wait long for some editor to adopt the new sideloading functionality in nREPL 0.7 -
`iced-vim` 1.3 did this only a couple of days after nREPL was released.

You can check out `iced-vim`'s [sideloader documentation](https://liquidz.github.io/vim-iced/#sideloader) for more details.

So, which client will be next?

## Chlorine Adds Alpha Support for nREPL

After the demise of Proto REPL, [Chlorine](https://atom.io/packages/chlorine)
has been the undisputed Clojure ruler of the realm of Atom.  Chlorine has
historically relied on `unrepl` to power its functionality, but the recently
released [version
0.5](https://github.com/mauricioszabo/atom-chlorine/blob/master/CHANGELOG.md#050)
added alpha support for nREPL!

Chlorine's author, Mauricio Szabo, wrote a couple of cool blog posts on the topic that I can heartily recommend:

* [nREPL on Chlorine](https://mauricio.szabo.link/blog/2020/04/01/nrepl-on-chlorine/)
* [Implementing a nREPL Client](https://mauricio.szabo.link/blog/2020/04/04/implementing-a-nrepl-client/)

I'm pretty sure Chlorine's support for nREPL will improve a lot in the months to come.

## Conjure Adds Support for nREPL

In other (amazing) news - [Oliver Caldwell](https://github.com/Olical) has been working on a [rewrite of Conjure](https://github.com/Olical/conjure-sourcery),
that's powered by nREPL and may (will?) support other programming languages besides Clojure!

The project is off to a very promising start and I'm very excited about it!

## Calva's new Debugger

Calva, CIDER's dear sibling, now has an [interactive debugger](https://clojureverse.org/t/calva-gets-a-debugger/5751)!
It utilizes `cider-nrepl` and VS Code’s debugger extension API. This work has been done as part of the Clojurists Together Q1 2020 funding period.

I'm super excited to finally see another editor reusing CIDER's debugger, and I'm looking forward to seeing which editor will be next!

## Babashka Now Ships with a Native nREPL Server

These days everyone has been raging about the awesome [babashka](https://github.com/borkdude/babashka), that
increased the reach of Clojure almost as much as ClojureScript did a few years ago. Scripting in Clojure is a dream come true for some many people!

The industrious Michiel Borkent (a.k.a. `borkdude`) recently unveiled [native nREPL support](https://github.com/borkdude/babashka/releases/tag/v0.0.79)
for `babashka` in version 0.0.79. What's more - he did such a great job that the new server works pretty well with CIDER
and you'll even get code completion! Michiel shared with me earlier today [a short presentation](https://www.youtube.com/watch?v=0YmZYnwyHHc&feature=youtu.be) he did for the Amsterdam Clojure User Group on the subject.

Somewhat amusingly, all of this was achieved with [231 lines of code](https://github.com/borkdude/babashka/blob/master/src/babashka/impl/nrepl_server.clj). I think that's a great testament to the simplicity of nREPL.

## Arcadia's nREPL Server is a New Hope for ClojureCLR

Early on nREPL was ported to ClojureCLR. That port, named [nREPL
CLR](https://github.com/clojure/clr.tools.nrepl), was a 1:1 reimplementation of
the Clojure version, and was off to a pretty strong start. Unfortunately, this
port was abandoned relatively quickly, and probably doesn't even work these days. The lack of an
nREPL server for ClojureCLR meant that its users could not take advantage of many of the most
popular editor plugins and IDEs for Clojure, but this is finally changing.

[Arcadia](https://github.com/arcadia-unity/Arcadia)'s team have implemented a pretty solid [nREPL server for the CLR](https://github.com/arcadia-unity/Arcadia/blob/master/Editor/NRepl.cs). I'm hoping that as it matures it
will lead to a much improved access to top-notch Clojure dev tooling for ClojureCLR. I'm hoping that the server will be extracted from Arcadia's codebase soon and will be distributed as a standalone project for the benefit
of the entire ClojureCLR community.

Even though the server is written in C#, its entire code is something like 500 lines of code. If you're into ClojureCLR I'd encourage you to check out the project and try to help.

## nREPL for ClojureScript

I guess most people know that nREPL has supported ClojureScript for ages via [Piggieback](https://github.com/nrepl/piggieback). Piggieback is a pretty
cool solution, but it comes with a few quirks and with one important limitation - it doesn't work with self-hosted ClojureScript, as it's written in Clojure.
This means that if you're into Lumo and Planck you can't use with them with your favourite nREPL client. This, however, might change soon as well...

[Chris Badahdah](https://github.com/djblue) has been working for a while on a
[native nREPL implementation in
ClojureScript](https://github.com/djblue/nrepl-cljs), which won't have this
limitation. If you're into ClojureScript, I'd encourage you to help him with the
project.

## Misc

Let's wrap things up with several smaller news:

* The next Leiningen release [will bundle nREPL 0.7](https://github.com/technomancy/leiningen/pull/2673).
* I've updated [REPL-y](https://github.com/trptcolin/reply) to ship with nREPL 0.7. A new release is pending.
* I've extended nREPL's [documentation on building clients](https://nrepl.org/nrepl/building_clients.html).
* I've started some documentation on [building nREPL servers](https://nrepl.org/nrepl/building_servers.html).
* I'm planning to start work on nREPL 0.8 soon. Built-in code completion and info lookup will be my primary focus.

As usual - I welcome help with everything nREPL-related! Don't be shy, we've got fun, challenging and rewarding
tasks for everyone!

## Epilogue

What a wild ride! I don't know about you, but so much new things happening in nREPL's world
feels like Christmas to me! I think it's reasonably safe to say that nREPL's community is bursting
with energy and ideas these days and I'm very optimistic about the future. We've achieved a lot already, but
I feel the best is yet to come...

Keep hacking! May the (n)REPL be with you always!
