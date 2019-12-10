---
layout: post
title: State of CIDER 2019 Results
categories: posts
tags:
- Clojure
- Emacs
- CIDER
- Survey
- Meta Advent 2019
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

It took me a while to write this article, but it's finally here.
I've opted to group related questions into several broader themes.
Here we go...

**Note:** You can find all the raw data from the survey [here](https://docs.google.com/spreadsheets/d/15AxpE16AmxaAhg6tbKnxl4L6OrEVTdnL5V0c5fxuWFk/edit?usp=sharing). I couldn't figure out how to share with everyone the diagrams (mostly pie charts)
that I got from Google Forms directly with you. If someone know how to do this - please, let me know.

<!--more-->

## A Dedicated User Base

I was surprised to learn that the majority of CIDER's users (around 50%) have been using it for more than 3 years.
In fact less than 15% of all respondents said that they started using CIDER in the past year.

I'm not sure whether those results are good or bad, but it's good to know that many people enjoy CIDER so much that
they stuck with it for quite a while in a highly competitive environment. Certainly today we've got way more
great alternatives to CIDER than we did 5 years ago.

Of course, this might be also related to the fact that 60% of respondents said that they were using Emacs before.[^1]
This was also quite surprising, as I always thought we had plenty of people who came to Emacs for CIDER. Perhaps
many of those have fled to other tools now that there are more of those. I can only speculate here.

Another interesting insight for me was that 13% of respondents came to CIDER from vim and around 12% from Cursive.
I wasn't that surprised about the vim converts, as Emacs has a pretty good vim keybindings emulation[^2], but
I didn't expect any Cursive converts.

## User Environment

Most people seem to be using either macOS or GNU/Linux (around 53% for both). Windows users are quite few at
around 6%. Note that some people are using more than one operating system.

90% of all respondents are working with Emacs 26 or 27 (current development version), we've got only 10%
still running Emacs 25. 2/3 of all users reported they had a custom Emacs setup, and 28% are running Spacemacs. Frankly, I expected
more people on Spacemacs. It was also nice to see that about 7% reported they were using my Emacs Prelude.

Around 50% of all people reported they were running the latest stable CIDER, with 28% running the development version
of CIDER. Funny enough 8% responded that they had no idea what version of CIDER they were using.
Most people reported that they were updating CIDER after each release and that the upgrade process is pretty
painless these days. Great success!

I was quite happy to learn that these days 97% (!!!) of all users are relying on `cider-nrepl`. I expected
this to be something like 70-80%, but this result really surprised me.

## Typical Usage

Another interesting insight for me was that the bulk of our users (2/3) are using CIDER for both
Clojure and ClojureScript development. The rest are doing Clojure-only. ClojureScript support
has always been a weakness of CIDER, although things have been improving there gradually in the past couple
of years. I guess knowing how many people rely on that support should motivate us even further to improve it.

When it comes to build tools, 2/3 of CIDER's users are relying on Leiningen and
about 1/3 on `tools.deps`. All the other build tools barely registered in the
survey. I was surprised that Boot fared so poorly in it (less than 3%).

87% of all participants in the survey are targeting Clojure 1.10. The number is pretty similar for ClojureScript
1.10 (84%). There are still some people targeting 1.8 and 1.9, but it seems that Clojure 1.7 is pretty much dead.
Seems our decision to try to support the last 3 Clojure(Script) releases was a good one.

About 2/3 of our users are typically starting CIDER using `cider-jack-in` and
the rest favour the use of `cider-connect`.  No surprises here. Most people
(70%) use CIDER only for local development, but about 30% of the users said they
were also using CIDER to connect to remote applications.

## Extensions

2/3 of the respondents said they were using some CIDER extensions. `clj-refactor` was the most popular
by a huge margin - almost 90% usage. `helm-cider` came in second with 10%. None of the other extensions
made it to 10%. Over the years CIDER absorbed many extensions, so I guess it's not a big surprise that
people are relying much on extensions.

There were also quite a few remarks that people didn't even know that many of those extensions existed,
even though all of them are listed in [CIDER's manual](https://docs.cider.mx/cider/additional_packages.html#_cider_extensions).

When it comes to Emacs extensions - `paredit`, `smartparens`, `projectile` and `magit` were the top choices
for most people. I doubt that anyone is surprised by those results.

## Favourite Features

When asked to highlight their favourite features in CIDER most people pointed out the following:

* The debugger
* The (value) inspector
* Interactive programming

I was surprised to see so many mentions of the inspector. It's great to hear so many people find it useful!
Relatively few features were highlighted by most people, so I'm also wondering how many of our users are
familiar with everything CIDER has to offer. Another way to interpret this is that perhaps we have
too many useless features and we should trim our fat. :-)

## Ideas for Improvement

When it comes to ideas for improvements the front-runners were:

* Better ClojureScript support
* Better find-usages
* Improved refactoring functionality/Merging CIDER and clj-refactor
* Improved support for `clojure.spec`
* Fewer breaking changes
* More informative messages (especially when it comes to errors)

By the way, we've been planning to merge CIDER and clj-refactor for a while now, but unfortunately our progress
on that front has been quite slow. I can assure that we're working to some extent on all those topics, but with our
very limited bandwidth big improvements take their time.

It also seems that most people are reasonably happy with CIDER's defaults as there weren't many suggestions
to improve those. Prompting for a symbol prior to acting on it and switching focus on certain actions popped
up a few times on this question.

## Learning and Resources

The vast majority of respondents seem to be happy with the current state of CIDER's documentation.
I've spent a lot of time working on it throughout the year, so I'm glad to see this.

When it comes to support resources, the Clojurians Slack is clearly number 1 (favoured by about 50% of the respondents).
Reddit, GitHub Issues and StackOverflow come next with more or less the same share - about 12%-13%.

I try to monitor all of them, even though I often ignore some questions simply due to lack of time.
I always appreciate it when others are helping out with the support questions.

## Funding

The bulk of people (70%) said they were using CIDER at their job. On the other hand only 22% said that
they or their company are supporting CIDER. That's certainly a bit disappointing, but it's relatively
easy to explain - most companies need some licences that they can buy and this option doesn't exist for them.

On the bright side - 83% responded that they would be willing to support CIDER to one degree or another. I'll
use this opportunity to remind everyone that the best ways to fund the development of CIDER today are:

* [Open Collective](https://opencollective.com/cider)
* [GitHub Sponsors](https://github.com/sponsors/bbatsov)

These days I'd recommend using GitHub sponsors if you want to pitch in, because
they don't charge me anything and for the first year they're matching the
donations that I receive (if you donate $100, I'll receive $200). In general I
prefer Open Collective's model, as it's focused on the project, not on a
particular person, but I certainly wouldn't mind getting a bit of extra money
from GitHub/Microsoft for free.

All in all, it's nice to see people are willing to invest in CIDER. I'll just have to figure out
at some point how to sell some licences for it to companies.

## General Notes

This was the first survey of the type that I ever did and it was an useful learning experience for me.
I got a lot of feedback about improving the structure and the type of the questions.

I promise you that I've learned my lessons and that the next survey is going to be better!

I've also noticed that people seem to confuse CIDER and clj-refactor functionality, but
that's not a big surprise.

## Closing Thoughts

Well, that's wrap!

545 people participated in the survey in total, which was a bit
underwhelming as I really hoped we'd be able to make it to 1000, but it is what it is.
We'll go big next year!

I think that overall the survey results show that CIDER is in a good place and
has a dedicated (and happy) user base. People want to see existing features polished and
refined, more stability in the project (CIDER 1.0 maybe) and better ClojureScript support.
Your voice has been heard and I hope that we won't let you down!

As usual there are many ways to interpret [the data](https://docs.google.com/spreadsheets/d/15AxpE16AmxaAhg6tbKnxl4L6OrEVTdnL5V0c5fxuWFk/edit?usp=sharing) and this is
simply mine interpretation. I'm looking forward to seeing more articles from members
of the community analyzing it and sharing their perspective!

Thanks to everyone who participated in the survey! I really appreciate your
input and I'll work hard next year to make CIDER even better!

[^1]: And unlikely to ever use a different editor.
[^2]: And there's also Spacemacs...
