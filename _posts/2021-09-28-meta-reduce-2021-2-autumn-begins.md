---
layout: post
title: 'Meta Reduce 2021.2: Autumn Begins'
date: 2021-09-28 11:58 +0300
tags:
- Meta
- Clojure
- CIDER
- RuboCop
---

It's been a while since my last general update. The summer is now behind us, and
autumn is in full swing. I don't know about you, but for me historically autumn
has been a pretty productive season. I am, however, still on my sabbatical (long
vacation) and I spend most of my time traveling and relaxing. That being said,
there's has been plenty of progress with my projects.

## RuboCop

There has been a new RuboCop release every couple of weeks (give or take) for
the entire summer. While there's nothing significant worth highlighting here, I
think we've made great progress across the board. I'm especially happy that [cop
safety is now documented a lot better](https://github.com/rubocop/rubocop/pull/10094).

## Emacs

I've submitted most of my Emacs projects to [NonGNU ELPA](https://elpa.nongnu.org/nongnu/). CIDER is the only important
project missing at this point. I'll submit it there after its next stable release.

I've also started to pay more attention to the `emacs-devel` mailing list, after mostly ignoring it for years.
It's challenging, but insightful to be involved there.

Outside of this and CIDER, I had very little time for my Emacs projects. I'd definitely like to spend
some time on Projectile before the end of the year.

## CIDER & Friends

I already wrote about [incomplete]({% post_url 2021-08-17-introducing-incomplete-a-simple-clojure-code-completion-library %}) a while ago. I'm happy to report that it replaced `clojure-complete` in both REPLy and Leiningen, so my work here is done. It was
fun to work on a small library. I had forgotten how that felt.

For CIDER the big news is undoubtedly that [now it supports library and middleware
sideloading](https://github.com/clojure-emacs/cider/issues/3037), which means that any connection can be "upgraded" on the fly by CIDER to
provide the full range of CIDER functionality.
That's big! Huge thanks to Arne for working on that functionality!

`clj-refactor` and `refactor-nrepl` have been getting a lot of love by `vemv`,
and I guess we'll be ready for a great release in a couple of weeks. I think it's
safe to say that the projects hadn't seen so much activity in a very long time. At this pace
the issue trackers for both projects will be empty by Christmas!

`cider-nrepl` and `orchard` have also been getting some attention
(e.g. performance improvements, making APIs more consistent) and will likely
have new releases soon.

Things have been quiet on the nREPL front, apart from work related to implementing sideloading support in CIDER. Right now it seems nREPL 0.9
might land in the second part of October.

Last, but not least, [I (CIDER) was selected to receive one year of funding from "Clojurists Together"](https://www.clojuriststogether.org/news/long-term-funding-selections/)! That
was totally unexpected, and it means a lot to me! Thanks to all the members of
"Clojurists Together" for their support! Cheers!

## Misc

Here are some random personal updates:

- I traveled to Corfu, Athens, Tuscany, Bologna, Milano and Lago di Como during September. I haven't had so many trips in so little time in ages!
- I've switched from Chrome to Firefox for my personal browsing. Firefox is awesome! I still plan to use Chrome for work.
- I've switched from LastPass to 1Password and Bitwarden. That's not a mistake - I like them both, so I use them both, at least for the time being. 1Password has better UI/UX, but I like the simplicity of Bitwarden and the fact I don't need a desktop app with it.
- I've been pondering whether to upgrade my trusted old iPhone XS. I love it, but its battery life leaves something to be desired.
- I've been a super happy Fastmail user for several months now.
- I've read a ton of books. I can highly recommend "The Cult of We".
- I've started watching the new season of "Sex Education". It's awesome!
- I've started to re-watch Daniel Craig's James Bond movies. 2 down, 2 to go.
- I achieved over 100 1vs1 wins with Protoss in the current StarCraft II season. I still suck, but I guess I suck a bit less.
- I've been playing a bit with [HEY World](https://world.hey.com/bozhidar). I like it, I just have to figure out what to use it for. `TooManyBlogsException`!

## Epilogue

That's all from me for now. I'll be mostly on the road until mid-October, so expect some slowdown around my OSS projects, but I hope that once my long vacation is behind me, I'll be more productive than ever. Keep hacking!
