---
layout: post
title: RuboCop Defaults Survey Results
date: 2020-05-26 09:56 +0300
categories: posts
tags:
- Ruby
- RuboCop
---

A while ago [we ran a survey with RuboCop's users]({% post_url
2020-03-27-adjusting-rubocop-s-defaults %}) with a the goal to decide if some
defaults needed to be updated before cutting RuboCop 1.0. 722 people took part
of the survey, and while this number was somewhat disappointing, given the size
of RuboCop's user base, I hope the results are representative of the
general sentiment of our users.

In this article I'll briefly summarize the results from the survey and provide some
personal thoughts on them.  I'm too lazy to include some nice charts here (and
it's hard to share directly those generated by Google Forms), but I hope you'll
forgive me.

## Single-quoted vs double-quoted string literals

This is without a doubt the favourite bike-shedding topic of the Ruby community.
I've wasted countless hours of my life listening to the arguments of both sides,
and I still remain convinced that it doesn't really matter much which style you're
adopting as long as you apply it consistently. For the record - my personal preference
has always been double-quoted string literals.[^1]

The results on this question definitely surprised me:

* Single-quoted strings - 58%
* Double-quoted strings - 39%
* Other (e.g. that they don't care about this) - 3%

Before the survey I assumed this was going to be one default we might need to
change, as for as long as RuboCop existed some (many) people were vocally
complaining that RuboCop's default (single-quoted string literals) wasn't
reflecting the actual preference of the Ruby community.  It seems, however,
that's not the case. It's possible that RuboCop influenced this over the years,
I don't know. At any rate now it's clear that we're not changing this particular
default.

## Maximum Line Length

Another question with surprising results (at least for me):

* 120 characters - 48%
* 100 characters - 21%
* 80 characters - 18%
* Other (e.g. 150, no limit) - 13%

I definitely didn't see this coming and I expected some close contest between 80
and 100. Even though I feel that 120 is a bit extreme, your voice has been heard
and this default has been adjusted in RuboCop 0.84. I didn't go as far as recommending
120 in the [Ruby Style Guide](https://rubystyle.guide), but I've amended the [line
length guideline](https://rubystyle.guide/#max-line-length) to be more
permissive of longer line limits. I've also added there a bit of rationale why we shouldn't
go overboard with the line length.

## Trailing Commas in Collection Literals

``` ruby
# no trailing commas
{ one: 1,
  two: 2 }

# only in multi-line literals
{ one: 1,
  two: 2, }

{ one: 1, two: 2 }

# always
{ one: 1,
  two: 2, }

{ one: 1, two: 2, }
```

I hope the code snippets above illustrate clearly the question. Here are the results:

* I don't use trailing commas - 64%
* I use trailing commas only in multi-line collection literals - 27%
* I use trailing commas in all collection literals (including single-line literals) - 1,5%
* Other - 7,5%

Here the results are a bit messed up, as I split the question about "trailing commas" in two after launching the survey,
based on some feedback I got that the original formulation was unclear as to whether it covered single-line literals or not.
At any rate, "no trailing commas" won by a landslide and this default is going to stay the same.

## `and` and `or`

The question of whether `and` and `or` are useful or should be totally avoided has long captivated the Ruby community.
Here's what RuboCop users had to say about them:

* Don't use them at all - 68%
* Use them all the time - 4%
* Use them for flow of control (e.g. `do_something and return`) - 29%

Despite the results, however, we opted to make RuboCop more permissive here and
to allow `and` and `or` for flow of control, mostly because this style seems to
be quite common in Ruby on Rails. Probably most people won't even notice this
change.

## Double Negation (`!!`)

Double negation is one of those idioms that are both useful and confusing (especially to newcomers) at the same time.
Let's see what's the verdict on it:

* Only when I need to return a boolean (e.g. in a predicate method) - 53%
* Don't use it at all - 39%
* Other - 8%

Once again your voice has been heard and double negation is now allowed in `return` context.

## Spaces inside hash literals

``` ruby
# with spaces
{ batman: "Bruce Wayne" }

# no spaces
{batman: "Bruce Wayne"}
```

* I use spaces - 71%
* I don't use spaces - 29%

No surprises here.

## Parentheses around Kernel method arguments (e.g. `puts`, `system`, `exit`)

``` ruby
# no parens
puts "Hello, world!"
exit -1

# with parens
puts("Hello, world!")
exit(-1)
```

* I don't use parentheses - 80%
* I use parentheses - 17%

No surprises here as well.

## Do you find the Metrics cops (e.g. `CyclomaticComplexity`) useful?

* Somewhat useful - 43%
* Very useful - 29%
* Useless - 19%
* Other - 9%

Seems that's one area that could be substantially improved. More about this in the next section.

## General level of happiness with RuboCop's defaults

* Very happy - 15%
* Happy - 47%
* OK - 23%
* Not happy - 8%
* Very unhappy - 5%

Not great, but not terrible either. I hope the changes we made in RuboCop 0.84 and that we plan to make down the road
will improve the overall satisfaction with the defaults.

## General Feedback

This section of the survey was pure gold and we got a ton of great feedback there. Most of the feedback could probably be summarized as:

* The value of many cops is somewhat subjective and it'd be nice if RuboCop had some cop preset with only really essential cops (e.g. Lint cops and those that map to the strongest conventions and idioms)
* The value of the metrics cops is questionable. There were many suggestions to relax certain defaults there.
* Many people seem to hate frozen string literals :-)

There was also the usual healthy dose of disagreement with some Layout defaults, but that was something totally expected.

At any rate - we're definitely going to tackle the cop presets idea at some point and provide a smaller set of "essential" cops,
alongside the current somewhat heavy-handed default set of cops.

## Sidenote

When I saw that many people were complaining about the Metrics cops I added an extra question asking if people knew they can
disable entire cop departments via `.rubocop.yml`. Turns out that 40% of the RuboCop users didn't know that. Here's how this can be done:

``` yaml
Metrics:
  Enabled: false
```

You can read more on the subject [here](https://docs.rubocop.org/rubocop/configuration.html#generic-configuration-parameters).

## Raw Data

Here's the [raw data](https://docs.google.com/spreadsheets/d/1OE41zxJ6oXaV1h_BlwwQWvTxmykjPm4iGVPG6DE0MvA/edit?usp=sharing) from the survey for those of you who'd like to analyze it further.

I'd love to hear how others interpreted it.

## Epilogue

Thanks to everyone who participated in survey! I hope this article and the changes we've made to the defaults in RuboCop 0.84
show you that we respect the opinion of our users and that your voice has been heard.

Right now the release of RuboCop 1.0 is imminent and we're unlikely to do anymore last minute changes, but there will always be 2.0, 3,0, etc. Keep hacking!

[^1]: So much for the theory that RuboCop's defaults are simply my subjective preferences.