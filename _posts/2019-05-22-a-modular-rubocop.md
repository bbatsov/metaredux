---
layout: post
title: A Modular RuboCop
date: 2019-05-22 08:58 +0200
categories: posts
tags:
- Ruby
- RuboCop
---

Last year at RubyKaigi I spoke about [the past and the future of
RuboCop](https://www.youtube.com/watch?v=nrHjVCuVsGA). This talk marked the
birth of the [RuboCop HQ organization](https://github.com/rubocop-hq)
and the first time I spoke about RuboCop 1.0. There were 3 primary
areas of work I outlined for RuboCop 1.0 back then:

* Safe auto-correct (that doesn't break your code)
* Safe upgrades (that don't introduce new offenses)
* Modular design

We've already made a lot of progress on the first point and I wrote about that
[a while ago]({% post_url 2018-10-27-a-safer-rubocop %}). Safe upgrades
are currently [in the works](https://github.com/rubocop-hq/rubocop/issues/5979) and I expect we'll wrap them up fairly soon.

Today I'm going to focus on modular design. What do I mean by modular design?
Historically RuboCop has bundled Ruby style checks, Rails style checks and
performance checks. This worked out well for a while, but as the project
grew bigger and more popular the number of the checks in each department
skyrocketed and it became harder and harder to keep track of everything.
There's also the fact that the Ruby style checks are fairly stable, but the
Rails and performance ones are constantly in flux, as many of them are
relevant only to specific versions of Rails and the Ruby runtime.
Naturally, there's also the problem of not being able to release updates to Rails
or performance checks independently of RuboCop itself. For me it had become
clear that we were way past the point of the "majestic monolith" and we had
to take some measure to prevent things from escalating any further.

So, what are we doing about all of this? The solution is pretty
simple - we've decided to break the original RuboCop into 3 gems:

* [RuboCop](https://github.com/rubocop-hq/rubocop)
* [RuboCop Performance](https://github.com/rubocop-hq/rubocop-performance)
* [RuboCop Rails](https://github.com/rubocop-hq/rubocop-rails)

RuboCop Performance went live recently, and in RuboCop 0.68 we've
removed all performance cops from RuboCop. Today I'm happy to announce
the inaugural release of RuboCop Rails! RuboCop 0.71 will issue
a warning about the pending removal of Rails-related cops from the
core project and they will be deleted in RuboCop 0.72. Note that the initial
version of the `rubocop-rails` gem is 2.0. It might seem a bit odd, but there's
a [good story](https://github.com/toshimaru/rubocop-rails/issues/31) behind this.
You are advised to switch to the new gem right away - it will take precedence
over the Rails cops bundled with RuboCop itself. The core project will stop
updating the Rails cops that are part of it effective immediately.

This announcement is the culmination of an entire year of hard work by
the amazing [Koichi Ito](https://github.com/koic), who spearheaded the
extraction of both the performance and the Rails cop departments. Going forward
Koichi will be the primary steward of the new gems, while I'll keep my
attention mostly on the core project. I've long wanted to focus more on
improving RuboCop's internal, performance and default configuration, and I hope
my reduced responsibilities will allow me to spend more time ruminating on how
to build a better RuboCop core experience.

The extraction process had one nice side effect - it helped us improve
RuboCop's internals and provide a better extension API in
general. There's a lot of work left on the extension API front,
but things are certainly moving in the right direction.  The RuboCop
ecosystem has been steadily growing for the past several years and I
hope that soon we're going to see more great additions to it
(e.g. `rubocop-minitest`, `rubocop-hanami`, etc). Fingers crossed!

I know this transition means another (somewhat) painful upgrade for
some of you, but I promise you that the pain is going to be worth
it. We're now at a striking distance from RuboCop 1.0 and the era of
stability it should bring. I'll write a bit more on that subject in
the weeks to come. That's all I have for you today! Keep hacking!
