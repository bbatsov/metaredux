---
layout: post
title: Farewell, Grimoire
date: 2019-06-29 16:43 +0200
category: posts
tags:
- Clojure
- Documentation
---

A few days ago [Reid McKenzie](https://github.com/arrdem) announced over twitter that he was shutting down Grimoire - a popular alternative to [ClojureDocs](https://clojuredocs.org/) he had created.

<!--more-->

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Grimoire&#39;s deprecated. Traffic now forwarded to clojuredocs, and the domain will be recycled for something eventually. Thanks everyone for your support, eyeballs and kind mentions of the project over the years. <a href="https://t.co/ImOMbEGppR">https://t.co/ImOMbEGppR</a></p>&mdash; actually OPS (@arrdem) <a href="https://twitter.com/arrdem/status/1144065851370299392?ref_src=twsrc%5Etfw">June 27, 2019</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

As I said a couple of years ago, when I was speaking about [awesome Clojure documentation](https://www.youtube.com/watch?v=nrpsMB2gYI0) at ClojuTRE 2017, the big problem with documentation is that everyone demands it to be great, but (very) few people would take the time to actually make that happen. That's why I've always been super appreciative of any efforts in the docs space and I want to take a moment to honour the legacy of Grimoire.

Grimoire was created at a time when ClojureDocs had stagnated and hadn't been updated for years, but it was more than a mere ClojureDocs clone.[^1] I really appreciated the logical way in which the essential information was presented on the home page, the improved search and the fact that ClojureScript was also featured there. Grimoire addressed one of the biggest shortcomings of ClojureDocs for me personally - it provided an API. That made it simple to integrate Grimoire into editors and show there directly the Grimoire docs for some var. Last, but not least - the super cool and easy to remember domain `conj.io`. I don't know about you but I've mistyped `clojuredocs.org` a million times as I kept forgetting was it `clojuredocs.org`, `clojuredoc.org`, `clojure-docs.org` or something else.[^2] Probably Grimoire improved on top of ClojureDocs in other areas as well, but those are the ones that I remember.

Perhaps most importantly - Grimoire's early success prompted ClojureDocs to finally update its own content for newer Clojure releases. Maybe the two events were not really related, but maybe a little bit of competition is not a bad thing and really drivers progress. I guess only Zachary Kim (ClojureDocs's author) can shed some light here.

You might be wondering why did Grimoire fail if it was so good? I don't really know, but I attribute its failure mostly to inertia and to Page Rank. It seems most people were satisfied with ClojureDocs and didn't really bother to look for an alternative and that Grimoire didn't score very high on Google for queries like `clojure map doc`.[^3]
There's also the fact that with time Reid's involvement with Clojure was reduced and eventually he moved on to focus on other things. Consider this post a tribute to his legacy in the world of Clojure as well - he was always experimenting with novel ideas and trying to push things forward. I don't know about you, but I certainly consider him a Clojure Hero! Thanks for everything you did, Reid! You're awesome!

What's next? Reid expressed hope that the end of Grimoire might lead to some consolidation of efforts for ClojureDocs. I hope that's going to be the case, but ClojureDocs seems to be struggling itself. It hasn't gotten much activity in the past 2 years and it still doesn't have Clojure 1.10 support, nor any ClojureScript support. An API, that would make it easier to interact programmatically with ClojureDocs would be great as well. I wonder if Zachary Kim would be open to moving the project under [Clojure Commons](https://clojars.org/clj-commons), so it'd get more attention and support there. It's an important part of Clojure's ecosystem and I'd love to see it thrive.

I also feel that [cljdoc](https://cljdoc.org) might be able to step up and fill the vacuum left by Grimoire. Martin (cljdoc's author) mentioned plans to add user submitted examples at some point and I'm really excited about this. cljdoc gained a lot of momentum since it launched and is probably the most important API documentation service in the Clojure ecosystem today. Depending on how things play out it might eventually supersede ClojureDocs and become the only Clojure API documentation service. I said earlier that competition is often a good thing, but given the limited resources for Clojure documentation projects I'd rather funnel those into a single project and make it truly great, as opposed to ending up with a half a dozen half-baked projects.

I hope this short article is going to provoke a few of you to think about the important topic of documentation and consider helping out projects like ClojureDocs and cljdoc. Now I'll wrap up and go back to writing Clojure documentation myself.[^4] Farewell, Grimoire! You'll be missed, but not forgotten! Let's make your legacy live on!

[^1]: <https://arrdem.com/2014/07/11/of_mages_and_grimoires/>
[^2]: There are way too many Clojure Docs project in existence.
[^3]: I think I never saw Grimoire in Google search results.
[^4]: Just kidding. I'm on vacation this week, but I still felt like writing the post.
