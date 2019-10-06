---
layout: post
title: 'Meta Reduce 2019.4: A Recap of a Busy Summer'
date: 2019-10-06 11:40 +0300
categories: posts
tags:
- Clojure
- Meta
- CIDER
- Ruby
- RuboCop
---

It has been quite a while since my last "Meta Reduce" post. I'm pretty disappointed with myself
for dropping the ball on "Meta Reduce" so quickly, but given how busy my schedule was the entire
summer I think I'll give myself a pass.

Long story short - the period since my last update was dominated by conferences,
travel, lots of work, and pursuing my newfound passion for personal finance
management. Now, let's dive into some of the highlights.

## Clojure

CIDER got funded by [Clojurists Together](https://www.clojuriststogether.org/)
and I've spent most of my OSS hacking time working on it lately. This resulted
in the recent release of [CIDER 0.22]({% post_url 2019-09-12-cider-0-22 %}) and
I'm currently pretty close to releasing CIDER 0.23 (a relatively minor update
compared to 0.22). My goals for the funding cycle are pretty humble - small
improvements here and there, better documentation, and better collaboration with
other tool authors. I hope to find the time to expand on this subject in a
separate article.

All of the projects in CIDER's Orchard have been making steady progress the past few months and I feel
we're really close to the release of new nREPL release. The only thing that remains to be done before we can
cut nREPL 0.7 is the [sideloading support](https://github.com/nrepl/nrepl/pull/162). Once that's in
I'll focus on CIDER implementing it. This is going to be big!

## Ruby

RuboCop got a pretty [big
release](https://github.com/rubocop-hq/rubocop/releases/tag/v0.75.0) recently
and brand new extensions for
[minitest](https://github.com/rubocop-hq/rubocop-minitest) and
[rake](https://github.com/rubocop-hq/rubocop-rake).  I haven't had much time for
RuboCop lately, but I plan to focus on the 1.0 release once I'm done with the
Clojurists Together funding cycle for CIDER.

I wrote a bit more about `rubocop-minitest` [here]({% post_url 2019-10-04-rubocop-meets-minitest %}).

## Conferences

I had a great time at Heart of Clojure (as a co-host) and Clojure/south (as a
speaker). Heart of Clojure was a really special event for me and I truly believe
it was one of the best Clojure conferences I've ever attended. Everyone looking
to create a good conference should [learn from Heart of
Clojure](https://lambdaisland.com/blog/2019-08-09-fork-this-conference).
Clojure/south was pretty good as well and I dedicated CIDER 0.22 to it and all
the lovely people I met there.

Next weekend I'll speak at a local conference in Sofia for the first time in a couple of years. I'm quite excited to be
back at [HackConf](https://www.hackconf.bg/en/) after a very long hiatus! Afterwards I've got just one final conference
appearance until the end of the year - the [Pivorak Ruby Conference](https://pivorak.com/) in Lviv, Ukraine.

This will wrap another busy conference season for me and I'll finally be able to focus on OSS work (and getting some rest).
I believe that's going to be the first time in 5 years I don't go to any events in November and December, which makes me a bit
sad, but we all run out of juice from time to time.

## Hardware

Even though I work remotely and spent a lot of my time in Zoom calls, for some
reason I never invested in a proper webcam. I bought two good condenser mics
early on in my remote career, but I've been using my shitty built-in laptop
webcam for 5 years now. After numerous jokes from my colleagues that I look like
an ominous shadow on most calls (because my desk is right by a window) I
finally decided to get a new webcam. After a week of research I chose the [Logitech
C925e](https://www.logitech.com/en-us/product/c925e-webcam) and I'm really
pleased with its performance so far. Works flawlessly on macOS, requires no
setup at all and has a built-in privacy shade.  I might write a bit more about
it later.

I also finally bought a DisplayPort cable capable of delivering 4K video at
60Hz, as ever since I got my new 4K display earlier this year I've been stuck at
30Hz due to limitations of my 5 year old laptop's HDMI. I can only tell you that I
should have gotten that cable much earlier! 30Hz are kind of acceptable if you
only use a computer for work, but everything looks a bit surreal at that refresh
rate.

In general I'm more and more convinced that it's time for me to buy some new computer, but I'll wait a few more months
to see what the holiday season is going to bring to the table. Currently [this AMD-powered fanless desktop engineered in Germany](https://www1.cirrus7.com/en/produkte/cirrus7-incus/)
seems quite appealing to me. If only they were making those with Zen 2 processors...

## Real World

Since my last update I've managed to visit 5 countries, made some new friends, reconnected with some dear old friends, attended two weddings and had a lot of fun.
Definitely the time I've spent in Brazil was the highlight of my travels and I'll forever cherish some fond memories of Clojure/south and the week I spent afterwards with some colleagues
near Recife.

When it comes to books I didn't make much headway, but I did score a couple of personal wins.
I finally managed to read "Crime and Punishment" and I'm pretty close to wrapping up "The Intelligent Investor", so I'm quite pleased with myself. I've been planning to read those two books for a very very long time.

I've also been practicing my Spanish by watching "La Case de Papel". I have to admit that I enjoyed it way more than I expected to. I should find more (good) shows in Spanish!

Last, but not least, I've spent a lot of time building and nurturing my investment portfolio. I've adopted a simple strategy based on foreign ETFs and some local "dividend" companies.
Let's see if the imminent Brexit is going to wreak havoc to it. I'm mostly worried that half the fintech and brokerages operating in the EU are based in the UK and it's still not clear
what kind of impact will Brexit have on them.
