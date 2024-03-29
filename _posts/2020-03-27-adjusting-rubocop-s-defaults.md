---
layout: post
title: Adjusting RuboCop's Defaults
date: 2020-03-27 11:24 +0200
categories: posts
tags:
- Ruby
- RuboCop
---

Since the beginning of time certain people have been unhappy with RuboCop's defaults.
I guess that's one of the universal truth in life - no matter how hard you try you can
never please everyone. Especially the users of a lint tool.

Still, with RuboCop 1.0 just around the corner, I've decided to run a [short survey](https://docs.google.com/forms/d/e/1FAIpQLSe3uTB_E164hgGUaM5-DFuIVBgv7l18ub2lAU5gXmw1dw4vPQ/viewform?usp=sf_link) so we
can see if some defaults really don't make sense. The survey covers only the topics that I remember had generated
the most heated discussions throughout the years (e.g. single-quoted vs double-quoted strings), but I've also added an open-ended question that allows you to share
whatever else frustrations you might have with the default configuration.

We've avoided changing the defaults in the past couple of years (part of our general effort to reduce the friction of RuboCop's upgrades) and we'll really limit those after RuboCop 1.0. It seems that's our final opportunity to make some sweeping changes and
make people royally pissed.

I'll leave the survey open for a couple of weeks[^1] and might potentially
extend the list of questions if I notice some trends in the answers to the
open-ended question. Once the survey is closed I'll publish the results
online, as I assume they will be of interest to many people.

Note that changes to RuboCop's defaults will be made only if the survey results
indicate a significant difference between a current default and its alternatives
(e.g. >10%). If we're looking at numbers like 48% to 52% it's not worth doing
any updates as any of the values would be just as good of a default. No point in
breaking anyone's setup without a solid reason to do so.

Please, share the survey around - the more responses we get, the better our
actions are going to be.  That's everyone's simplest way to influence the future
direction of RuboCop!

That's all I have for you today! Keep hacking!

[^1]: Currently I plan to close the survey on the 12th of April, but this date might change depending on daily number of the respondees by then.
