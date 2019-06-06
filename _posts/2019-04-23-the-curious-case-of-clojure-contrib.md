---
layout: post
title: The Curious Case of Clojure Contrib
date: 2019-04-23 20:18 +0300
categories: posts
tags:
- Clojure
---

Do you know what's the common thing between Clojure Contrib,
microservices and DevOps? Everyone thinks they know and understand
them, but most people have a different idea about what they are.[^1]

For many years people have believed that Contrib is one of the following:

* A staging ground for functionality that will eventually make its way to Clojure.
* A standard library for Clojure.
* A set of (superior) libraries maintained/blessed by Rich/Cognitect.

Contrib has a [long
history](https://clojure.org/news/2012/02/17/clojure-governance) and I
think that in the course of it the vision for the project
changed a few times.[^2] Unfortunately for quite a while the only official
description of Contrib was one really [outdated
article on Clojure's Confluence](https://dev.clojure.org/pages/viewpage.action?pageId=5767464).

I've wanted to share my perspective on Contrib for a very long time, as I felt
it had a somewhat harmful effect on the broader Clojure community, but I've been
putting this off as I don't enjoy writing such articles much.
I finally found the inspiration that I needed after a recent
conversation I had [on
GitHub](https://github.com/clojure/clojure-site/issues/348) with Sean
Corfield, Alex Miller and Stu Halloway about Contrib's philosophy. I
won't reproduce here everything that we discussed there - after all
you can just go and read the discussion or peruse the excellent new
[documentation](https://clojure.org/community/contrib_libs) that
conversation resulted in.

<!--more-->

## The Nature of Contrib

So what is Contrib, really? Let's see what the official documentation has to say
on the subject:

> Clojure is an umbrella level project that hosts many additional
> libraries called Clojure contrib, which all exist under the Clojure
> organization on GitHub.
>
> These libraries use the same contribution model (Contributor
> Agreement, license, and copyright) as Clojure itself, and a shared
> infrastructure maintained by the Clojure core team:
>
> * Source control - Git libraries on GitHub
> * Issues - JIRA
> * Development model - patches in JIRA, no PRs
> * Continuous integration - Jenkins
> * Doc generation and hosting - Autodoc and GitHub pages
> * Builds - Maven with deployment to Maven Central under the groupId org.clojure
>
> Every project has one or more owners (often community members) who
> determine the direction of their library, triage patches,
> etc. Project owners have full commit rights and authenticated access
> to their project on the build box for releases.
>
> Contrib libraries are open source libraries, like any other open
> source Clojure library. They vary widely in development activity and
> quality and may or may not be the best alternative for their
> functionality in the overall ecosystem. You should evaluate them
> before use as you would any open source library you intend to use as
> a dependency.
>
> -- Official Contrib Libraries Documentation (https://clojure.org/community/contrib_libs)

I think that's pretty clear. As you can see Contrib libraries follow very closely
Clojure's own development model and share its licensing. You should also note
there are no guarantees about the quality and maturity of those libraries.

> I think generally too much is made of contrib. It's just a
> collection of libs with a shared dev model. That's no different than
> clj-commons or ClojureWerkz. They all have different approaches but
> each has its own view of how to setup and manage projects.
>
> There is no "goal" for contrib beyond providing infrastructure for
> lib dev. It's just a bunch of libs with a shared dev model.
>
> -- Alex Miller

Even clearer.

## A Personal Perspective

Now that we're on the same page about what Contrib is, I'll switch
gears a bit and share some personal perspective on it, which is
why I've started writing this article in the first place.

First things first - I'm certainly bothered a bit by the lack of an
overarching goal that drives Contrib. It's hard to be making progress
if you don't know where you're going, right?  Likely all of this is
just an artifact of the history of Contrib, but it irks me
regardless. I actually attribute a lot of other things that I'll
discuss in the paragraphs to come to this general lack of vision and
direction.

I've always been a bit frustrated that there's no clear distinction
between projects in Contrib which ended up there for historical reasons
(e.g. as part of the restructuring of the original monolith
`clojure-contrib`), projects that were community-contributed (e.g. `tools.trace`), and
"special" projects that are run by Cognitect themselves (e.g. `core.async`,
`tools.deps`, etc). To add further to the confusion there are some
`core.something` and `tools.something` projects started by
Rich/Cognitect and some that are not (`core.async` vs `core.logic`,
`tools.reader` vs `tools.deps`, etc).  It's really hard to tell at a
glance which are the "Core Clojure" projects in there and which are
the community contributed/maintained.  That's not a big deal for me, as
I've followed Clojure's development closely since version 1.0, but I
know it is a serious source of confusion for casual onlookers. If it
were up to me I'd just move the "non-Cognitect" projects to a different
GitHub org (e.g. `github.com/clojure-contrib`) and keep only the core
projects in the current organization.

By the way, writing this reminded me that we don't even have the proper
terminology for something like `core.async`. In theory it's just
another library, but in practice its impact is huge and no one really
tried to replicate `core.async`, despite a lot of complaints about
known problems and slow development pace (likely because there is still
some expectation that it might end up being part of Clojure at some
point). I also can't tell you how to refer to something like
`tools.deps` - it's definitely not an ordinary community project, but
it's also not a true core project like `clojure.spec`. Anyways, for
lack of better terminology I'll refer to all "special" libraries as
"Core Clojure" in this article.

I think Cognitect are aware of this "blurred lines" problem as
recently they have been favouring using their company's GitHub orgs for
non-core Clojure libraries (e.g. `transit`, REBL, etc). I also haven't
seen any new non-Cognitect-driven projects join Contrib in a very long
time, which is a good thing in my book.

If there's one area where Cognitect could have done better it's
community building, and engaging more actively Clojurians to help with
the growth of the Clojure ecosystem.  Here I'm not talking about engaging
people to work on Clojure itself, but rather on libraries, frameworks,
development tools and all that jazz. This might be just some wild
speculation on my end, but I really feel that if the community
stewards encouraged more actively work in certain areas it's pretty
likely that people will rally around their cause. Believe it or not leaders
wield considerable motivational power. :-)

In this line of thinking, the biggest problem with Contrib is that its
contribution process failed to attract contributors to most projects
hosted under it. Typically the projects would see activity while their
original author is involved in them and would stagnate
afterwards. Even extremely popular projects like nREPL,
`tools.namespace` and `core.logic` were affected by this.  Currently
only 11 out of 42 Contrib libraries are considered in active development with the
majority of the libraries considered to be in a "stable"
state. Unfortunately there's relatively thin line between _stable_ and
_abandoned_... While all of the libraries that are marked as stable are
certainly useful in their current form, there's also the fact that
few of them made it to 1.0, which carries the implication that their
author(s) felt that they hadn't reached the point of stability yet.
On the other hand, it's definitely true that abandoned libraries have
very stable APIs. :-) I also strongly believe that active development
and stability are not fundamentally at odds with one another. I think
Clojure itself is a perfect example of this.

I'm not going to try to make the argument that the Clojure's
contribution process is bad in general - it's what Rich wants for
Clojure and that's obviously his decision to make. Still, it seems
strange to impose it on projects where he and Cognitect are not really
involved. And if the CA is the only really important aspect of
Contrib - well, there are certainly less painful ways to enforce it
(e.g. you can have a CI check for PRs that verifies the PR author has
signed the CA).  Going down the rabbit hole, however, you really start
to wonder what's the point of having some libraries under the same CA
as Clojure if they are not essential in some manner. I definitely
think that all of this is just a remnant of period in time where it
was actually a possibilities for some of those external libraries to
be promoted to Clojure itself.

The high bar to contribute has likely affected negatively most
projects in Contrib and this goes well with the previous point I made -
essential and non-essential projects have different impact and
goals. The development rigidity required for something like
`tools.deps` is probably not required for something like
`tools.namespace`.  A separation between the two types of Contrib projects
might open up the way for the non-essential projects to be developed
in a more "relaxed" manner, while still remaining part of Contrib.

I'm assuming that by now many of you, dear readers, believe that I'm advocating for some
changes to Clojure Contrib. Let me pause here for a second to assure you that's
definitely not the case. I've resigned myself to the fact that Contrib is not
going to change and I've made my peace with this.

The real question that I want to ask is do we even need Contrib
today?  Are there any benefits it provides for the projects there at
all? The point of this post is
certainly not to teach Cognitect how to run Contrib and we're finally
getting to it.

## Beyond Contrib

I believe it'd be really great if people
stopped looking up to Cognitect to provide them with all the answers
and all the solutions, and embraced the fact that their bandwidth is
limited and their goals and priorities are not always going to be
aligned with yours. That's perfectly OK. If we all accept this the solution
to the problems I've outlined so far becomes apparent - the needs a community
are generally going to be served best by community-driven organizations.
Organization that share your perspective and your priorities.

There have already been some community-driven success stories in the
past (e.g. [ClojureWerkz](http://clojurewerkz.org/)) and we recently
saw the creation of [Clojure Commons](https://github.com/clj-commons),
which aims to host, develop and maintain key libraries in the Clojure
ecosystem.  That's a fantastic initiative and I'm certain it's going
to play a vital role in Clojure's future! But it's just one step
in a long journey. I'm a big believer in laser focused organizations
that house related projects targeting a particular
problem/domain. Think [Ring](https://github.com/ring-clojure),
[nREPL](https://github.com/nrepl), [Clojure
Emacs](https://github.com/clojure-emacs).  It's much easier to build a
team of like-minded people when the scope of something is clearly
defined. We need a lot more of those!

The people who know me are aware of my immense passion for [free
software projects](/projects) and that I always want to build
healthy communities around my projects, nurture contributors,
etc. I hope that the Clojure community will be dominated by such a type of
organizations down the road.

And that's the real point I've been trying to make all along - there's
a huge difference between community-driven projects and projects with
community involvement.  Clojure Contrib is not a community-driven
project. Plain and simple. For any community to mature and evolve it
needs to take control of its destiny.  That's equal to switching from
a mentality of following to leading. From waiting for something to
happen to making things happen. From complaining to fixing and
building.  That's where I want to see the Clojure community several
years from now. Trust me when I tell you that it's both more fun and
more empowering!

## Epilogue

So, let's summarize the key take always:

* Contrib is just a common way to develop Clojure libraries.
* It's pretty unlikely that any of the projects there are ever becoming a part of Clojure.
* There are no guarantees about the quality and maturity of the libraries.
* There's no real reason for most projects to join Contrib.
* There's a big difference between community-lead projects and projects with community involvement.
* It's high time for the Clojure community to take stronger ownership of its future.

Contrib played an important part in the history of
Clojure, but I think we're at a point where it has outlived its
usefulness and should be considered mostly a curious artifact of the past.[^3] I'm
certain that with time most of the non-Cognitect libraries there will
be migrated elsewhere (e.g. to Clojure Commons) or will be replaced by
alternative libraries, developed outside of Contrib. I can also hope that my article
and the success of nREPL, following its migration outside of Contrib, will expedite this process
and inspire more of you to participate in community-driven projects.

I've just noticed that Sean Corfield is considering to continue to the development
of `java.jdbc` [outside Contrib](http://corfield.org/blog/2019/04/21/next-jdbc/), which
I see as another encouraging sign that the times are changing. Hopefully other library
maintainers will move in this direction as well. Fingers crossed!

Keep hacking!

[^1]: Usually the wrong idea.
[^2]: Chas Emerick told me once the only reason he agreed to transfer nREPL to Clojure Contrib was a promise that it can end up as part of Clojure once it stabilizes.
[^3]: And I'm really partial to Sean Corfield's opinion he expressed [here](https://github.com/clojure/clojure-site/issues/348#issuecomment-461676330).
