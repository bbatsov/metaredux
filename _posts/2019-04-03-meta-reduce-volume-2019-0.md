---
layout: post
title: Meta Reduce, Volume 2019.0
date: 2019-04-03 11:46 +0200
categories: posts
tags:
- Clojure
- nREPL
- Meta
- Emacs
- CIDER
- Ruby
- RuboCop
---

## Prelude

For a while now I've been thinking of starting some form of online
journal, following mostly my open-source work and programming
exploits, but not limited strictly to that. I know that a _blog_ is
supposed to be just that (an online journal), but many of the things
I'm working on are not really worthy of dedicated blog posts (and
won't fit in a tweet), and still I feel that they deserve to be
shared with a broader audience.[^1]

Lately every edition of Alex Miller's excellent [InsideClojure
Journal](http://insideclojure.org/2018/12/14/journal/) has been a
constant reminder for me to get something done about my idea. And
finally here we are - "Meta Reduce" is my version of such a journal -
a reduction of my recent activities in writing.  I'm not quite
certain on the exact format yet, but I guess I'll sort it out
along the way.

It's very hard for me to write this first post simply because a lot
has happened since I returned from my OSS "sabbatical" about a month
ago. A lot of things have piled up in my absence and I literally don't
know where to begin. Here we go...

<!--more-->

## Ruby

I spent a lot of time going over RuboCop's [open pull
requests](https://github.com/rubocop-hq/rubocop/pulls) and I hope to
get them down to 5-10 eventually. After I merged a fair bit of them I
cut a [new
release](https://github.com/rubocop-hq/rubocop/releases/tag/v0.66.0).
I'll try to cut a new one later this week.

I would definitely appreciate more people helping out with the review
of incoming tickets and PRs, as it's really hard to keep track of
everything that's going on at this scale. Lately, I've rarely checked
the issues and focused most of my attention on almost exclusively on PRs.

RuboCop's next release is going to be pretty special, though, as we are
officially deprecating the bundled performance cops in favour of the new
[rubocop-performance](https://github.com/rubocop-hq/rubocop-performance)
gem.  That's part of the strategy towards RuboCop 1.0 - the
development of a robust API for extensions and moving the performance
and Rails stuff out of RuboCop's core. The performance cops will be
removed from RuboCop in version 0.68.

One more thing on the RuboCop's front - I recently found
[rubocop-daemon](https://github.com/fohte/rubocop-daemon) and it looks
like an interesting option for people who have been frustrated by
RuboCop's slow boot time. Its documentation says:

> Now VS Code will use the rubocop-daemon-wrapper script, and formatOnSave should be much faster (~150ms instead of 3-5 seconds).

If that's true it's quite impressive! And we really have a big performance problem to solve. :-)
I'll definitely look into this at some point, as it seems like something that shouldn't be hard to optimize.

I blogged on a couple of Ruby-related topics for the first in a long time. Namely:

* [The Missing Ruby Code Formatter]({% post_url 2019-03-30-the-missing-ruby-code-formatter %})
* [Ruby's Creed]({% post_url 2019-04-02-ruby-s-creed %})

Finally, I've been working on a new presentation dedicated on Ruby 3
for the [RubyDay conference](https://2019.rubyday.it/) in Italy.
Perhaps the talk will also inspire a few blog posts on the
subject. We'll see. The most important thing for me is to meet the
members of the local Ruby community.

## Clojure

nREPL and CIDER both had major releases recently and I wrote a couple
of articles on the subject:

* [nREPL 0.6]({% post_url 2019-03-29-nrepl-0-6 %})
* [New York, New York]({% post_url 2019-04-01-new-york-new-york %})

nREPL landed a very important feature
just a couple of days ago - the addition of a much-anticipated [EDN
transport](https://github.com/nrepl/nrepl/pull/135). I hope this will appease the people who have been
criticizing nREPL for it's lack of a built-in alternative of Bencode.

**Note:** Now we also have Transit-based transports in [Fastlane](https://github.com/nrepl/fastlane).

I'm pleased to report that almost all of the Clojure-related
projects I maintain were migrated from Travis CI to Circle CI. So far
I'm really impressed by the improved stability and performance of
Circle! I really wish I carried out this migration earlier. Special
thanks to [Shen Tian](https://github.com/shen-tian), who did a ton of
work on this and the EDN transport!

Earlier this week I published to MELPA
[parseedn](https://github.com/clojure-emacs/parseedn) - an EDN parser
for Emacs. I hope that I'll be able to leverage it in CIDER pretty
soon. I'll also use this opportunity to remind you we also have
[parseclj](https://github.com/clojure-emacs/parseclj). It can
potentially unlock some smarter refactoring and static analysis that's
baked straight into `clojure-mode`/CIDER. Arne Brasseur created both
projects a while ago, but I haven't had much time to play with them
yet. I hope that's going to change this year.  Feel free to play with
them as well!

I was reminded recently of the excellent
[emidje](https://github.com/nubank/emidje) plugin for CIDER. It
basically provides you with the same functionality you'd get in CIDER,
when it comes to `clojure.test` or frameworks implementing
`clojure.test`'s protocol. If you're into Midje you should check it
out!

As part of [Heart of Clojure](https://heartofclojure.eu/)'s program
committee I had the good fortune to participate in the final selection
of talks for the conference. There
were some many super awesome ideas to choose from that I really regret we
could pick only a handful of them. I guess the list of speakers will
be publicly announced really soon, but I can tell you now that I'm
100% certain the conference is going to be top notch.  By the way, I'll be the
conference's co-host (together with Saskia Lindner) and I'd like to
invite everyone reading this to join us there for an event to
remember!

Last, but not least - I'm currently putting the final touches on my upcoming
talk at the [Dutch Clojure Days conference](https://clojuredays.org/), that's going to take
place on Saturday.  I'm super excited to meet my old Clojure friends
and meet a few new ones! See you in Amsterdam!

## Haskell

I'm once again trying to level up my Haskell knowledge. I started
learning Haskell for the first time a decade ago, but for some reason[^2]
I never got very far with the language.  This
time around [The Haskell Book](http://haskellbook.com/) inspired me to
give it another shot. I've promised myself I'm not failing again, but time
will tell. So far, I'm really enjoying the book (even if it's a bit
slow paced for my taste) and I can heartily recommend it to everyone interested in
learning Haskell.

I have to say I'm really impressed by the quality of the Emacs tooling for Haskell. It's almost as good as Clojure's in certain aspects
and that makes playing with Haskell twice as fun. I'll probably write about this separately down the road.

## Meta Tech

I finally got myself an [external 4K
display](https://www.lg.com/us/monitors/lg-27UK850-W-4k-uhd-led-monitor)
and I totally love it.  I think the LG 27UK850 offers a great value
for its price and I would definitely recommend it. I spent months
researching all the possible options on the market and I'm glad that
I've managed to put making this decision behind me.

The only problem for me is that getting this monitor reminded me how
old my primary laptop is (almost 5 years old, no USB-C, no HDMI 2.0).
I'm starting to think it's time for a new computer. I've been a macOS
user for the past decade, but I'm really unhappy with Apple from a
hardware perspective lately and I wonder if I shouldn't go back to
using Linux. Frankly the only Apple computer I'd consider buying today
is the new 6-core Mac Mini, but I think that something like a Hades
Canyon or Bean Canyon NUC will probably serve me better. If only I
wasn't a fanatical AMD devotee looking for an excuse to go back
home... Haven't had an AMD-based machine for a very long time now. The
last one was sporting an Athlon Thoroughbred-B 2400+ something like 15
years ago. Damn, how much I loved that machine!

Anyways, seems I'm bound to waste a lot of time ruminating on this subject.
Researching/buying/building gadgets and computers is one of my favourite pastimes!

## In the Real World

I used much of my time off to catch up on reading. I've been so busy
with work and side projects the past several years that I had read
almost no books, and this was a big disappointment for a book lover
like me. I've read exactly [12
books](https://www.goodreads.com/user_challenges/15398098) since the
beginning of the year and I'm quite pleased with myself in that
regard.[^3] I focused mostly on sci-fi classics, that I didn't get to
read yet for one reason on another, but I managed to spicy up my
reading list here and there.  Most recently (yesterday actually), I
completed "The Dark Tower V: Wolves of the Calla". Now I have to
figure out what's next for me, so suggestions would be welcome.

I also managed to find a bit of time for another favourite pastime of
mine - movies and TV shows.  In my hometown of Veliko Tarnovo,
Bulgaria an iconic cinema was re-opened 30 years after it was shut
down. I had the opportunity to visit it and see there great movies
like the "Green Book" and "Coziness"[^4]. It felt really special to
witness the rebirth of a legend.

On the TV shows side I binge-watched "Narcos: Season 3", "Narcos:
Mexico" and the 7th and 8th seasons of "The Walking Dead". At this
point I seriously wonder why I keep wasting my time with "The Walking
Dead", as the recent seasons have been a far cry from the amazing show
it used to be.  I did enjoy "Narcos", though. The new seasons were not
as good as the ones focused on Pablo Escobar, but they were pretty
decent overall. Plus, these days I enjoy everything more if it's in Spanish.
Anyways, now I'm also accepting recommendations for the TV shows.

Unfortunately, I've made no progress with Spanish recently, with
sports, or with any of my other hobbies. Maybe next time.

But the thing I enjoyed the most in the past few weeks was simply being back home.
I needed a bit of boring routine in my life. And a bit of rest from my vacation. :D

## Epilogue

I guess this will do for now. I'm not winning any Pulitzers for this
post, but I hope some of you are going to find it useful. I know I'll
find it very amusing if I ever get to re-read it a few years from now.
Let me know what you think of the format and if you have any ideas on how to
improve it.

Until next time! Keep hacking!

[^1]: Twitter threads are also an option.
[^2]: Clojure.
[^3]: Especially given the fact that last year I read less than 10 books for the entire year.
[^4]: "Уют" in Bulgarian. Highly recommended.
