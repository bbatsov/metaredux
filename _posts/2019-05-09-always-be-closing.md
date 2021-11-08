---
layout: post
title: Always be Closing
category: posts
tags:
- Meta
- OSS
- Project Management
---

One of the biggest mistakes that an open-source project maintainer can do is to allow
the backlog of their projects to grow to a size where it's unmanageable. That something I've seen
many times and it always happens in more or less the same fashion, and leads to more or less the same
result.

You start a small personal project driven by your own interests and
needs. The scope of the project is tiny at the time - just a handful
of features, no plans for world domination. At some point the project
gains some traction.[^1] Users (understandably) start reporting bugs
that you never encountered and requesting new features you never
envisioned. You're excited that someone found your small hobby project
useful and you are eager to oblige and expand the scope to keep the
new users happy.  If you're extra lucky[^2] you start receiving lots
of pull requests from contributors who are willing to help move the
project forward. World domination is at hand!

Fast forward a few years into the future. Unless by some miracle
you've got all the free time in the world or your project became your
full-time job, you're probably facing now hundreds of open tickets and
dozens of open pull requests.  No matter how hard you try you can
never manage keep up with them. And you try really hard.  You're so
busy these days that you can't even keep up with the contributions
you're getting, let alone find some time to trim the backlog, label
tickets accordingly, respond to everyone and handle a couple of
tickets yourself.

And in those rare moments you find some time to peruse the backlog, so
you can pick some tickets for yourself, you're just getting depressed
by its sheer size (e.g. 100+ open tickets) and you don't know where to
begin. You spend all of your time considering which ticket to start
working on and then you're out of time. No progress. Lots of
frustration.

I happen to be the steward a [few somewhat popular
projects](https:/batsov.com/projects) and I'm saying all of this from experience.  I
know that focus is the key to success in everything, but I've been
finding it harder and harder to find my focus when it comes to OSS
projects, as so much work has piled on my plate there that I
constantly wonder where to begin. Time to make some changes.

Yesterday I've decided to enable a
[bot](https://github.com/apps/stale) that automatically flags stale
issues and pull requests on my major projects (e.g. RuboCop, CIDER,
Projectile) after a certain period of inactivity and closes them later
if they remain dormant. I always feel bad when I'm closing valid bug
reports and interesting feature requests, but I think it's much more
important to be realistic about what's actually feasible versus
relying on wishful thinking and giving fake promises. If a ticket is
not handled in a reasonable amount of time that usually means two
things:

* Probably it's not something super important.
* It's unlikely that it will ever be handled (lack of bandwidth, the maintainers don't feel it's particularly important, it's something super complicated, etc).

I've noticed that really important topics always tend to resurface and
there's always someone willing to tackle those. That's why I'm not
particularly worried about something major slipping between the
cracks, when relying on an automated ticket clean-up strategy.

I hope that the outcome of this experiment is going to be a clean and
focused roadmap for my projects resulting in better pace of
development and happier users in the end of the day. Time will
tell. At least I'm glad that I've finally tried to beat the vicious
cycle I was trapped in, as you know what they say about people who
keep doing the same things expecting different results...

Never forget that "less is more". Keep closing!

[^1]: No users, no problems.
[^2]: Or unlucky, depending on your perspective.
