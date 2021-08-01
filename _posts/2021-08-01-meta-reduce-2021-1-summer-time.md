---
layout: post
title: 'Meta Reduce 2021.1: Summer Time'
date: 2021-08-01 14:08 +0300
tags:
- Meta
- Clojure
- nREPL

---
It's been quite a while since my last "Meta Reduce" post.
I'm happy to report that since writing it in April, I got vaccinated against
COVID-19, and the cases in Bulgaria have dropped down significantly.
One can never know what life has in store for us next, but the summer
has been pretty pleasant and almost normal so far.

I finally did a bit of international travel for the first time since the pandemic started:

- In June I've spend a couple of weeks in Ukraine, visiting the family of my wife.
- In early July I finally managed to go to Venice, after a couple of failed attempts (the second one was in March 2020).
- In mid July I spent some quality time in Greece on the islands of Zakynthos and Santorini. If you're into peaceful vacations, I can't recommend the northern part
of Zakynthos highly enough!

Clearly I really missed traveling, as I think I overdid it in the past couple of months. Now it's time for a quiet month at home. I love Sofia in August, as
it's almost totally empty and very peaceful. I also hate traveling to the seaside during the high season. I guess I don't have a lot of fondness for big groups
of humans. Go figure!

There's not much I can report on the OSS front. I took a 1 month vacation from work in mid-July and I've been
trying to unwind a bit and stay away from programming for a while. Things are pretty quiet on the RuboCop and CIDER fronts.
[UNIX socket support for nREPL](https://github.com/nrepl/nrepl/pull/204) was finally merged a couple of days ago and I expect that relatively soon we'll release
nREPL 0.9 (e.g. early September).

We've also made some headway with the new library [enrich-classpath](https://github.com/clojure-emacs/enrich-classpath), that makes it
easy to download the sources and the javadoc for Java libraries. That's extremely handy for CIDER (Orchard) users, as it makes it possible
to navigate to the sources and javadoc of Java libraries. That has always been possible in CIDER, but the setup process was admittedly somewhat complex (you had to meddle with the classpath manually)
and `enrich-classpath` makes this simple. Kudos to `vemv` (I don't know his real name) for his work on the new library, and his recent efforts to improve
`cider-nrepl` and `refactor-nrepl`!

Another cool recent development is the launch of [nrepl-cljs-sci](https://github.com/viesti/nrepl-cljs-sci), created by Kimmo Koskinen. People have wanted an nREPL server
for Node.js for a very long time, and now they have one. The project is still in its infancy and will require a lot of polish and some documentation, but it's off to a very promising start.

I've used the extra time on my hands during my vacation to do some reading (I'm currently re-reading "A Wizard of Earthsea"), play StarCraft 2 (I totally suck there and random kids
wipe the floor with me), watch "Better Call Saul", [migrate my personal email from Gmail to Fastmail]({% post_url 2021-07-31-hasta-la-vista-gmail %}), and provide technical support for my parents (that requires a lot of time!).

I also plan to do a bit of extra blogging in the next couple of weeks, but with all the heat here, and my excessive laziness, there's no guarantee that this is going to happen. At least I finally finished the article I started to write last summer about the [brief return to Linux]({% post_url 2021-07-31-back-to-linux %}). I've got plenty of ideas for articles, though, and I'm optimistic that at least 20% of them are going to become actual articles eventually. Of course, there's always the possibility that I'll actually learn how to play StarCraft II, become a pro gamer and put programming behind me.[^1]

That's all I have for you today. Short and sweet. Enjoy the summer (winter)! May it be as normal and delightful as possible! Keep chilling!

[^1]: Don't worry, the chances of this happening are lower than those of England winning a major football trophy.
