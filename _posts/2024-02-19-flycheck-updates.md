---
layout: post
title: "Flycheck Updates: A new Stable Release and Way More"
date: 2024-02-19 10:30 +0200
tags:
- Emacs
- Flycheck
---

I've been pretty busy with Flycheck ever since [I became its maintainer recently]({% post_url 2024-02-10-the-state-of-flycheck-alive-and-kicking %}). Today I wanted to share with you a few of the recent highlights.

## Flycheck 34

I've cut [Flycheck 34](https://github.com/flycheck/flycheck/releases/tag/34.0), which features quite a few new checkers and removes a bunch of legacy checkers. As I'm not familiar with all the lint tools that Flycheck supports[^1], I'd appreciate your input on whether some checkers need to be updated/replaced/removed down the road.

**Random trivia:** I noticed that Chef's `foodcritic` has been replaced by [cookstyle](https://docs.chef.io/workstation/cookstyle/), a project built on top of my very own RuboCop project (a linter & formatter for Ruby) and I wrote the checker for it myself.

## `flycheck.org` Ownership

I've managed to obtain the ownership of the `flycheck.org` domain. Big thanks to Matthias Güdemann for paying for the domain after the departure of Sebastian from Flycheck.

## flycheck-eglot

I quickly realized that Eglot support was one of the main reasons why people switched from Flycheck to Flymake, so when I noticed that [Sergey Firsov](https://github.com/intramurz) had created Flycheck backend for Eglot I immediately invited him to move his project to the official Flycheck organization on GitHub. You can find `flycheck-eglot` [here](https://github.com/flycheck/flycheck-eglot).

## Documentation Updates

I've been updating the Flycheck [docs](https://www.flycheck.org/en/latest/) here and there. Most notably, I've updated the [comparison with Flymake](https://www.flycheck.org/en/latest/user/flycheck-versus-flymake.html), as a few people complained it was out-of-date.

I haven't used Flymake in ages, so I'm definitely not an expert there, but I did my best to make sure the comparsion is reasonably accurate. If someone notices any issues there - please, submit improvements!

## Open Collective

I've created an [Open Collective for Flycheck](https://opencollective.com/flycheck), so people and organizations can support the project financially via donations. I hope that in the long-run it can make the maintenance of the project more sustainable. In the short run - I'll use some of the donations to cover my expenses for `flycheck.org`.

## NonGNU ELPA

I've filed a request to submit Flycheck to [NonGNU ELPA](https://elpa.nongnu.org/), one of Emacs's official package repositories. Let's see how this will go. I'm hoping to have there Flycheck and many of its extensions in the long run.

**Update:** Sadly, Flycheck's NonGNU ELPA application [was rejected](https://lists.gnu.org/archive/html/emacs-devel/2024-02/msg00657.html). Supposedly it doesn't offer clear advantages over the built-in Flymake, so it shouldn't be included. It was also hinted that now Flycheck is obsolete and should be deprecated. A somewhat disappointing outcome, but definitely not unexpected. Oh, well - in MELPA we trust!

## Help Welcome

If you want to hack on Flycheck or any of its extensions - now would be a great time! I love working with other contributors and Flycheck could definitely use all the help it could get. The main organization features some 20 projects and not all of them are actively maintained today.

## Epilogue

And that's a wrap! It has been a busy few weeks working on Flycheck, but now I've done pretty much everything that I wanted to tackle originally. Moving forward I'll be working slowly on the backlog of issues and pull requests and pondering whether more ambitious changes will be needed down the road.

Thanks to everyone for their support for the project! Keep hacking!

[^1]: See <https://www.flycheck.org/en/latest/languages.html> for the list of built-in checkers.
