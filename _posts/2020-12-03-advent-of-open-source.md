---
layout: post
title: Advent of Open Source
date: 2020-12-03 13:18 +0200
tags:
- Meta
- Advent of OSS
- Emacs
- Clojure
---

Ah, it's again this magical time of the year - the Advent!
When I was little I associated it mostly with advent candy boxes
with a different candy for each day of the Advent, but these days
I mostly associate this period with the famous [Advent of Code](https://adventofcode.com/).[^1]

As much as I love solving puzzles, I've realized lately that probably
it's not the best use of my limited free time. Last year I opted to do
an [blogging challenge]({% post_url 2019-12-01-meta-advent-2019 %}), instead of the Advent of Code, and this
year I'm challenging myself to contribute something (meaningful) to my [open-source
projects](/projects) every day from the 1st of December to Christmas.
Fun times ahead!

Today is the 3rd of December already, so I guess I have to report what I've done so far.

## Dec 1

* Shipped [RuboCop 1.5](https://github.com/rubocop-hq/rubocop/releases/tag/v1.5.0)
* Lots of improvements to the [Clojure Style Guide](https://guide.clojure.style)
  * New sections on [guiding principles](https://guide.clojure.style/#guiding-principles) and [consistency](https://guide.clojure.style/#a-note-about-consistency)
  * Documented the [sources of inspiration](https://guide.clojure.style/#sources-of-inspiration)
  * Added lots of new code examples (e.g. [here](https://guide.clojure.style/#comments))
  * Fixed many headings that had broken wording after the automated conversion to the current structure last year (each guideline now has a title, and the titles were auto-generated from the old permalinks associated with the guidelines)

I hope to gradually clean up the backlog for improvements of the style guide in the months to come. I'll also mention here that I wouldn't mind
some volunteers willing to help document the best practices for working with `cljc` and `clojure.spec`.

## Dec 2

* Shipped [RuboCop 1.5.1](https://github.com/rubocop-hq/rubocop/releases/tag/v1.5.1)
* [Projectile](https://github.com/bbatsov/projectile)
  * Groomed the backlog (replied to some tickets, closed some tickets that were addressed/irrelevant, added tags to everything else)
  * Merged a few outstanding PRs (see the [changelog](https://github.com/bbatsov/projectile/blob/master/CHANGELOG.md#bugs-fixed) for details)
  * Lots of improvements to the documentation site
    * Improved [Usage](https://docs.projectile.mx/projectile/usage.html) page (better basic setup instructions, plus coverage of the mighty but elusive Projectile Commander)
    * Improved [Projects](https://docs.projectile.mx/projectile/projects.html) page (in particular, you'll find there a lot of information about the mythical and mystical `projectile-project-root-functions`)
    * Improved [Configuration](https://docs.projectile.mx/projectile/configuration.html)

It always feels great to revisit Projectile, as it's both the first open-source project I ever created and the project I use more than anything else.

## Dec 3 (today)

* More work on Projectile's documentation and grooming the backlog
* Preparing for another RuboCop bug-fix release (1.5.2 might land later today)

## Looking Forward

So, what's next?
My main priorities for the Advent of OSS are currently:

* Polish the Clojure Style Guide
* Ship CIDER 1.0
* Ship Emacs Prelude 1.1
* Clean up Projectile's backlog

I probably won't be able to achieve them all, but I've been told it's good to be ambitious. If someone wants to help me out, be my guest.
My projects have plenty of tickets tagged with `Good First Issue` that you might try tackling.

I'll try to post some updates here every few days, both to keep it
up-to-date with my progress and to keep myself honest. That's all from
me for now. Keep hacking!

**P.S.** In the time-honored tradition of the Advent, I'm inviting all of you to join my on the Christmas OSS adventure and see how far are you willing
to go! It will be fun!

[^1]: Half my Twitter timeline is about the "Advent of Code" these days.
