---
layout: post
title: "The RSpec Style Guide: Reloaded"
date: 2019-04-26 17:08 +0300
categories: posts
tags:
- Ruby
- RSpec
- Style Guide
---

I think for many years the gold standard for RSpec best practices has
been [Better Specs](http://www.betterspecs.org/). Eventually the
original site was abandoned and Better Specs found a [new
home](https://lelylan.github.io/betterspecs/) at
[Lelylan](https://github.com/lelylan). Further down the line it was
superseded by an RSpec style guide maintained by
[ReachLocal](https://github.com/reachlocal).[^1] Unfortunately at some
point the new style guide stagnated as well... I guess you sense where
this story is going, right? Fast forward to the happy end...

I'm delighted to announce that ReachLocal were kind enough to contribute the
RSpec style guide to the [RuboCop
HeadQuarters](https://github.com/rubocop-hq) organization, where it
joins the ranks of the popular and widely adopted
[Ruby](https://github.com/rubocop-hq/ruby-style-guide) and
[Rails](https://github.com/rubocop-hq/rails-style-guide) community
style guides.[^2] RuboCop HQ is becoming a focal point for all sort of
initiatives related to promoting and refining Ruby-related best
programming practices and that certainly pleases me, as this was the
single most important idea behind the creation of RuboCop HQ.

[Filipp Pirozkov](https://github.com/pirj) has assumed the
editor-in-chief duties for the RSpec guide and he updated it carefully
to match the structure of the Ruby and Rails guides, and recent RSpec
developments. The team behind
[rubocop-rspec](https://github.com/rubocop-hq/rubocop-rspec) is also
involved in the ongoing support of the guide. All in all - I'm
reasonably certain that RuboCop HQ is going to be its home for many
years to come. It's nice to break a vicious cycle, isn't it?

If you're into RSpec I encourage you to check out [the updated
guide](https://github.com/rubocop-hq/rspec-style-guide). I'm confident
that it's going to be a very useful resource for most RSpec
users. We're also really eager to get feedback and suggestions for
improvements from all of you! After all it's called a "community"
guide for a reason - it doesn't reflect the preferences of any single
individual, but the collective knowledge and experience of many members
of our fine community.

That's all great, but for me the real point of the story is something
completely different - all of this happened with almost no involvement
from me. That's the biggest testament to the fact that RuboCop HQ is
much bigger than me or any individual for that matter. Today there are
many style guide editors and many maintainers of the various RuboCop
projects. That's just another manifestation of that beautiful idea
that the Ruby community can achieve everything, when they set their
mind to it. No single points of failure, endless enthusiasm, lots of
hard work and a constant drive to do better. Seems that the community is
really big on solid coding standards and tools to enforce them these days, and
I'm happy that I played a small part in the early days of that
movement. I'm also super excited to see what the future holds for us!

Keep hacking! And keep writing beautiful specs!

[^1]: Frankly, I'm not quite certain about how things went down
    between Better Specs and ReachLocal's style guide. Please, forgive
    any inaccuraries in the background story.

[^2]: Technically speaking all of this happened about a year ago, but I completely
    forgot to do a proper announcement back then.

**Update (2019-04-28)** The article sparkled an [interesting
conversation on
Reddit](https://www.reddit.com/r/ruby/comments/bhnc1c/the_rspec_style_guide_reloaded/). Unfortunately,
there was a lot of bitterness and negativity in some of the comments,
so I'll take a moment to address those.

Both the new RSpec style guide and
[rubocop-rspec](https://github.com/rubocop-hq/rubocop-rspec) are
projects that could **really** benefit from your feedback and your
help.  There's no status quo, there's nothing set in stone, and we
truly value collaboration and seek broad consensus.  Unfortunately
achieving broad consensus on every topic is close to impossible, so
people should also understand that some compromises have to be
made. Adopting a hard stance against any efforts to standardize and
promote good stylistic practices doesn't help anyone. You can be part
of the problem or part of the solution - the choice is yours!

I also encourage everyone to check out Filipp's comments, as they are
quite insightful.[^3] The struggles that Better Specs experienced with
their custom publishing pipeline are a good reinforcement of the
decision we made to keep things as simple as possible going forward -
and nothing is simpler than publishing the guide as a single text
file. This approache served well the community Ruby and Rails style
guides, and I'm pretty sure it's going to work well for the RSpec
guide as well.  Is the end of the day I think it's much better to have
great content than beautiful visuals, right?[^4]

[^3]: Especially the one on the [history of Better Specs](https://www.reddit.com/r/ruby/comments/bhnc1c/the_rspec_style_guide_reloaded/elwm951), and how we ended up where we are today.
[^4]: Although having them both would be ideal for sure.
