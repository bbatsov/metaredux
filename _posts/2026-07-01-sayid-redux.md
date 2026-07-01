---
layout: post
title: "Sayid Redux"
date: 2026-07-01 15:00:00 +0300
categories: posts
tags:
- Clojure
- nREPL
- CIDER
- Emacs
- Debugging
---

I have a soft spot for [Sayid][] - it's one of the most ingenious Clojure tools
ever built, and also one of the most neglected. It's an *omniscient debugger*:
instead of stopping your program at a breakpoint, it quietly records every call
to the functions you've traced and lets you rummage through the recording
afterwards. It's the kind of thing you demo to people and watch their jaw drop.
And it had been sitting practically unmaintained for the better part of a decade.

Here's the awkward part: that neglect is largely on me. [Bill Piel][bpiel],
Sayid's original author, handed me the keys ages ago, and I've been... let's say
a less than exemplary steward. I'd merge the occasional patch to keep the lights
on, but until very recently I'd done precious little to actually move the project
forward.

> The best time to maintain your open-source project was six years ago. The
> second best time is now.
>
> -- Ancient proverb, lightly adapted

So what finally lit a fire under me? Blame [CIDER 2.0][cider-2]. I've been
reworking CIDER's debugging and tracing story, and at some point I sat down to
make the built-in tracer smarter. A few hours in it hit me: nothing I could
realistically bolt onto the built-in tracing would come anywhere close to what
Sayid already does. So instead of building a worse Sayid, I dusted off the real
one, gave it a good scrub, and here we are - [Sayid 0.4][changelog].

<!--more-->

## What is Sayid, anyway?

For the uninitiated: Sayid records the arguments, return value, timing and full
call tree of the functions you trace, so you can go back and inspect exactly what
happened - no breakpoints, no `println`, no re-running the thing five times.

While we're on the subject of embarrassing confessions: I've been the maintainer
of this thing for years and I *still* have no idea what "Sayid" actually means or
what it's a reference to. If you happen to know, please, put me out of my
misery - I'd love to finally get the joke.

Trace a namespace, run your code, and pop open the workspace with `C-c s w`:

```
▾ (demo.coins/can-afford? [:quarter :dime :nickel :penny] 45) => true
    (demo.coins/total-cents [:quarter :dime :nickel :penny]) => 45
```

`can-afford?` says `true`, but four coins worth 41 cents shouldn't cover a
45-cent tab. Something's off, and the bug lives inside `total-cents`. This is
where Sayid really shines - flip on an *inner* trace and it records every
expression *inside* the function:

```
▾ (demo.coins/can-afford? [:quarter :dime :nickel :penny] 45) => true
  ▾ (demo.coins/total-cents [:quarter :dime :nickel :penny]) => 45
    ▾ (apply + (map coin-values coins)) => 45
        (map coin-values coins) => (25 10 5 5)
```

There it is, staring right at us: `(25 10 5 5)`, when the last value should be
`1`. A penny is worth five cents in our coin map. We never wrote a single
`println`, and we never had to guess where to put a breakpoint - we just looked
at what actually happened.

That's the pitch, and it's a great one. So why was such a cool tool gathering
dust?

## Why an omniscient debugger, in 2026?

Honestly, part of it is that Clojure folks are spoiled. We have the REPL, so the
reflex for most of us is to just re-run a form with some `tap>` or `println`
sprinkled in and eyeball the output. That works right up until it doesn't -
until the bug is three layers deep in a `map` over a lazy seq, or only shows up
on the 400th call, or lives in code you didn't write and don't feel like
instrumenting by hand.

A traditional stepping debugger stops the world and makes you drive. Sayid does
the opposite: it lets the program run to completion and then hands you the entire
execution history as something you can navigate at your own pace.
[`tools.trace`][tools-trace] is in the same spirit, but it dumps text to
stdout - Sayid keeps *structured data* and gives you a query DSL to slice it. That's a much better fit for how we
actually work in Clojure: run it, capture everything, explore the data.

And here's the thing that made me want to revive it rather than reinvent it -
"capture everything as data and explore it later" is exactly the workflow that's
becoming *more* relevant, not less. Structured execution traces are gold, whether
the thing doing the exploring is you, a data-inspection tool like [Portal][], or
an AI assistant trying to understand why your code misbehaved.

## Out with the old coordinates

First order of business was dragging the project into the present.

Sayid used to live under `com.billpiel/sayid` on Clojars, with namespaces like
`com.billpiel.sayid.core`. The new home is [clojure-emacs][], so the artifact is
now published as `mx.cider/sayid`:

```clojure
{:user {:plugins [[mx.cider/sayid "0.4.0"]]}}
```

I also dropped the personal-domain prefix from every namespace - it's plain
`sayid.core`, `sayid.trace`, `sayid.nrepl-middleware` and so on now. The old
`com.billpiel/sayid` coordinates still get the same releases for the time being,
so nobody's dependency breaks overnight, but the future is `mx.cider`.

## Data first, strings later

Here's the change I'm most excited about. Sayid's nREPL middleware used to hand
the Emacs client a pre-rendered blob of text plus a pile of text properties for
colouring. In other words, the server did all the rendering and the client was a
dumb terminal. That single decision is a big part of why there was exactly *one*
Sayid client.

So the middleware now speaks data. There's a family of new ops -
`sayid-get-workspace-data`, `sayid-query-data` and friends - that return the
recorded call tree as honest, navigable data instead of a wall of text:

```clojure
{"id"       "4793"
 "name"     "demo.coins/can-afford?"
 "args"     ["[:quarter :dime :nickel :penny]" "45"]
 "return"   "true"
 "file"     "demo/coins.clj"
 "line"     12
 "children" [...]}
```

The structural bits (ids, names, timings, source location, the tree shape) come
across as real nested maps and lists you can walk by key. The captured values are
`pr-str`'d, since an arbitrary Clojure value can't always round-trip over the
wire - but that's the only place strings sneak in, and it's exactly where you'd
expect them.

The upshot: any editor or tool that speaks nREPL can now fetch a workspace and
render it however it likes, and a REPL one-liner or a Portal tap gets you the
same data with zero Sayid-specific machinery. The whole thing is written up in
[doc/nrepl-api.md][nrepl-api], so you don't have to reverse-engineer the wire
format from the Emacs client the way you would have before.

## A UI that doesn't feel like 2015

With the data ops in place, I rebuilt the Emacs UI on top of them. The workspace
and the "what's traced" views are now proper foldable trees, built on [CIDER][]'s
new `cider-tree-view`. You get real folding (`TAB`), navigation (`n`/`p`),
jump-to-source (`RET`), and - my favourite - `c i` hands the actual captured
value straight to CIDER's inspector, so you can drill into an argument or a
return value as a live, navigable object rather than squinting at its printed
form.

There's also a query layer wired into the tree: `f` narrows to every recorded
call of the function at point, `i` focuses a single call and its subtree. On a
big trace that's the difference between "wall of text" and "actually finding the
thing".

The best part is that the client is now *smaller*, not bigger - all the tree
rendering, folding and value inspection are handled by mature components instead
of bespoke code painting text properties by hand. That's the payoff of moving
rendering to the client: the server ships data, and the client is free to be as
fancy or as plain as it wants.

## Wait, you broke everything?

> Backwards compatibility is a promise you make to the users you have. Sayid had
> exactly one, and reader, I *am* that user.
>
> -- Me, rationalising

Yeah, I did. I went pretty wild with the breaking changes this time around - new
artifact coordinates, new namespaces, a reworked nREPL API, a bumped minimum
CIDER version. Normally I'd bend over backwards to keep old clients working, but
here I made a deliberate call: as far as I can tell, the bundled Emacs client was
the *only* client Sayid ever had. Dancing around imaginary third parties to
preserve compatibility nobody was relying on would have just made the project
harder to adopt and harder to maintain.

So I opted for sweeping changes that leave Sayid in a much better place to build
on, rather than a museum of backwards-compatible cruft. If it turns out I was
wrong and you were quietly depending on the old coordinates - I'm sorry, and do
let me know, because that's genuinely useful information.

## Take it for a spin

That's the gist of it. Sayid is alive again, it's leaner, it speaks data, and it
has a UI I'm not embarrassed to demo. What I'd love now is for more people to
actually *use* it and tell me whether the new direction resonates.

So please - `[mx.cider/sayid "0.4.0"]`, trace something gnarly, and pop open the
tree. Then head over to the [issue tracker][issues] and tell me what you think:
what feels great, what feels rough, what's missing. I have ideas for where to
take it next (bounding the recording so you can safely trace a whole namespace
under a test suite is high on the list), but I'd rather steer by what people
actually want out of it.

Big thanks to Bill Piel for building such a wonderful tool in the first place -
I'm merely standing on the shoulders of a giant here. And thanks in advance to
everyone willing to kick the tyres on the revival!

That's all from me for now. Keep hacking!

[Sayid]: https://github.com/clojure-emacs/sayid
[changelog]: https://github.com/clojure-emacs/sayid/blob/master/CHANGELOG.md
[nrepl-api]: https://github.com/clojure-emacs/sayid/blob/master/doc/nrepl-api.md
[issues]: https://github.com/clojure-emacs/sayid/issues
[clojure-emacs]: https://github.com/clojure-emacs
[bpiel]: https://github.com/bpiel
[cider-2]: https://metaredux.com/posts/2026/06/30/cider-2-0-is-brewing.html
[CIDER]: https://www.cider.mx
[tools-trace]: https://github.com/clojure/tools.trace
[Portal]: https://github.com/djblue/portal
