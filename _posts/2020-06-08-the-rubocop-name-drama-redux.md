---
layout: post
title: The RuboCop Name Drama Redux
date: 2020-06-08 09:52 +0300
author: Bozhidar Batsov, RuboCop's author
categories: posts
tags:
- Ruby
- RuboCop
---

By now, if you're part of the Ruby programming community, you've probably heard
of the [proposal to rename
RuboCop](https://github.com/rubocop-hq/rubocop/issues/8091) and the unpleasant
events that followed it.  I really struggled with coming up with a name for this
article, as there are many topics I wanted to touch upon (e.g. the burden of OSS
stewardship, being respectful and adhering to the norms that once defined the
Ruby community, creating a safe collaborative environment, the final decision
about the name, etc), but I'm quite limited in time right now. This generic
title is the best that I could, but I hope you will forgive me and focus on the
content instead. Once again I got reminded that naming is (very) hard.

I don't really want to go repeat/retell everything that has transpired. The GitHub
issue and the surrounding Twitter conversations should give you a good
idea. There's also a [nice
summary](https://timriley.info/writing/2020/06/08/rubyists-we-must-do-better/),
written by Tim Riley that you can peruse.  The purpose of the article you're
reading now is to provide some closure for that topic and a path for us to move
forward.

So, here we go...

<!--more-->

## My Reaction

Well, it's safe to say that I felt pretty bitter and antagonized yesterday, as I
had assumed that the lack of responses to my original message on the subject
meant that the issues suggesting to rename the project was resolved.[^1] Then
suddenly I woke up on Sunday morning to find a lot of messages on Twitter and GitHub,
and some of them (subjectively) felt aggressive and hostile to me.

I was quite disappointed by the hasty creation of two RuboCop forks, without discussing any specifics with our team.
There were also some generic snide remarks about my leadership style and me running away from responsibility that
definitely rubbed me the wrong way. In particular my desire to keep politics out of RuboCop generated a lot of flak.
On top of this I already had a very bad week and it seemed that things were bound to get even worse.

I've spent a lot of the day pondering on how exactly should I approach this and
in the meantime I was very pleasantly surprised by the amazing amount of support
I got over various channels. I was disappointed, however, that some people
adopted a disrespectful and aggressive tone, while trying to defend me. That's
not the Ruby way! Regardless of the circumstances and our disagreements, I'm a
big believer that we should always treat each other with consideration, empathy
and respect. As a result I completed the overdue adoption of a code of conduct
for RuboCop and had to lock and moderate a GitHub conversation for the first time (or
at least I feel that was the first time).

I apologize personally to everyone who experienced any vitriol and hostility as
part of the back and forth on GitHub and the various social media. I certainly
won't ever approve or tolerate such behaviour on any project that I steward or
I'm involved with in any capacity. As always I expect a lot from the Ruby
community and hope that we can do better. Still, often Ruby's dramas overshadow
its spirit if niceness, friendliness, collaboration and understanding.

I obviously could have done better myself. Despite my best efforts I did reply
with irony or sarcasm to a few snarky remarks on Twitter.  I was accused of
seeking sympathy and cheap publicity, but if you truly knew me you'd simply
know I've got a pretty short fuse and it takes great efforts on my end to
restrain my responses in situations I perceive as personal attacks on me.
I'm only human after all, and I've got my personality flaws like everybody else.

## RuboCop's Name

So, what are we doing about the name of the project?

RuboCop's going to keep its name going forward. I've already shared my arguments
on the subject and I won't repeat myself here.  RuboCop's Core Team support this
decision and we all feel that's the best course of action overall. One of our
responsibilities as stewards is to weigh the pros and cons of every proposal we
get and compromise when ideal solutions, that would make everyone happy, don't
exist. Given the fact that we couldn't even reach universal consensus on basic
things like the default configuration over the course of 8 years, one should
understand that finding solutions that everyone likes is close to
impossible. And that's OK.

That being said we're also committed to creating a safe collaborative
environment that everyone's comfortable with.  I felt that the approach suggested by
Zee Spencer to create a friendly highly compatible RuboCop fork called
[rbhint](https://github.com/zspencer/rbhint) solves the problem that was
outlined in the GitHub ticket in the least disruptive way. Here's how Zee described his
idea in brief: "My goal with my fork, rbhint, is not to replace RuboCop, but to
do the work to reframe the project for an American reality. Just like Ubuntu
doesn't replace Debian, just reframes it for home use."

That's certainly fine by me and I'd encourage people who share his perspective to help him out.
Consider this my official endorsement of his efforts.

I'd also like to remind everyone of RuboCop's sibling project
[Standard](https://github.com/testdouble/standard), which has been around for
quite a while.  While it has a slightly different philosophy compared [RuboCop's
own](https://docs.rubocop.org/rubocop/index.html#philosophy), its name is as
neutral (standard?) as possible and I think it's a great option for people who
don't have strong preferences on code style and would rather leave it to a tool
to pick some solid defaults for them.

As far as I'm concerned the topic of the project's name is closed at this point.
I hope everyone will respect our (RuboCop's team) decision and let us focus on
delivering a better tool for you. I also hope that our friendly and helpful
RuboCop might help restore some of the goodwill towards the word "cop" down the
road.

## OSS is Broken

Nothing new here - lots of pressure on OSS developers (especially on those
involved in bigger projects), little appreciation and support of their work,
lots of burnout and frustration as the (most common) end result.  That's not
the first time there have been toxic exchanges around RuboCop and personal
attacks on me from people who disagreed with my approach to stewardship.[^2] As
a result RuboCop has gradually become one of my least favourite projects, having
been my very favourite one when I started working on it in 2012. I wish everyone
would understand that OSS is a product of love and if you manage to siphon this
love out of a project you're killing its very essence.

It was fun re-reading Rich Hickey's essay [Open Source is not About
You](https://gist.github.com/richhickey/1563cddea1002958f96e7ba9519972d9).  When
the essay was first published I felt that Rich was a bit too negative and
disillusioned with his take on OSS development, but 2 years later I definitely
sympathize with his perspective a lot more. There's a strong culture of entitlement in the OSS world
and it's very detrimental in the long run as all it does is driving people away from their projects.

RuboCop has close to 100,000,000 downloads and I've easily spent on the project
2000+ hours of my time over the past 8 years.  I can only speculate how much
more time was spend by the other core contributors, but it's safe to assume
we're looking at something in the vicinity of 10,000.[^3]  RuboCop still has no
major corporate sponsors, and the donations it gets are a drop in the pond
considering all the time me and the other major contributors have put in the
project. Yet the expectations for RuboCop have kept growing all the time
(e.g. people expect a stronger focus on backwards compatibility, more
configuration flexibility, better performance). You don't need to be a math whiz to realize
that's not sustainable and people should either adjust their expectations or
their level of support (code/documentation contributions, donations, etc).

I find it weird and sad that the most of our sponsors are individuals (and we
love them for that support!), yet the companies that they work for, are the ones
truly benefiting from getting tools that empower developers to be more
productive. And they benefit from those tools for free. That's not a problem
specific to RuboCop, it has been well-known for a while now, but it's a super
painful problem regardless. Why weren't we discussing how to solve it instead of
changing the name of one tool?

Something really needs to change to break the current vicious cycle.

## Epilogue

I've never imagined that I'd find myself in the center of a Ruby drama of that magnitude, but here we are.
I've decided to treat this as a positive experience in the end of the day and focus on everything
new I've learned and could learn from it. I didn't decide it right away, though. That's why
I'm glad I took some time to gather my thoughts before writing this article.

A friend of mine joked earlier today that being the head maintainer of RuboCop is probably the most stressful
OSS "job" in the realm of Ruby. I'm pretty sure Matz has the most stressful job in our pocket of the programming universe,
but I was amused my friend's remark regardless and it stuck with me. I feel a lot of sympathy for everyone working
on lint-like tools!

I'm very grateful to everyone who voiced their support for RuboCop and me in particular. You should know
by now that you're the main reason why I'm still working on OSS projects. Special thanks to RuboCop's amazing
Core Team for their love and support!

That's all I have for you today.
Now it's time for us to go back to work and make RuboCop 1.0 happen. My goal is to ship it later this week.

[^1]: That's why I had closed the ticket. I responded I don't think we should rename the project, I waited for a day or two and as there were no more repsonses I thought this was it.
[^2]: A lot of people have strong opinions when it comes to code style and would argue quite ardently to support their perspective. Imagine being in hundreds of such discussions...
[^3]: The `sloccount` tool estimates that 31 person/years went into the development of the main RuboCop repo alone, which it puts at cost to develop of $4,200,000 (using an algorithm I don't quite understand). Regardless of the accuracy of those estimates that's a huge effort!
