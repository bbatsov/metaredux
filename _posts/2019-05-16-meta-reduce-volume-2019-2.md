---
layout: post
title: Meta Reduce, Volume 2019.2
date: 2019-05-16 10:32 +0300
categories: posts
tags:
- Clojure
- nREPL
- Orchard
- Meta
- Emacs
- CIDER
- Ruby
- RuboCop
---

About a month has passed since the last "Meta Reduce" update I wrote.
I've kept pretty busy in the mean time and now I have quite a few things to share with you.
Where do I begin...

<!--more-->

## Clojure

### CIDER

I've spent a lot of time lately triaging issues and cleaning up the issue tracker a bit.
In the course of this process I've identified quite a few [good first issues](https://github.com/clojure-emacs/cider/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22) and I've labeled them accordingly.
I hope that some of you would take a stab at fixing some of them.
I keep dreaming of a backlog of less than 50 tickets, but CIDER won't get there without your help.

I've addressed a few small tickets and I've implemented a prototype of find references based on ideas I published [earlier]({% post_url 2019-05-04-discovering-runtime-function-references-in-clojure %}).
The prototype is rough around the edges, but works reasonably well overall. You can try it with `M-x cider-xref-fn-refs`.

I've also played a bit with the Read the Docs setup for CIDER and it now serves the documentation for the latest stable version by default.[^1]

### Hard CIDER

I've started a new blog series dedicated to CIDER. Here are the articles in the series so far:

<ul>
{% for post in site.posts reversed %}
  {% if post.tags contains 'Hard CIDER' %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endif %}  <!-- tags if -->
{% endfor %} <!-- posts for -->
</ul>

I plan to write many more, but knowing me - I'll probably fail miserably. Time will tell.

Alongside it, I've created a [demo project](https://github.com/bbatsov/hard-cider) I'll be using the blog series.
It's basically empty at this point, but I hope it's going to become an useful reference for newcomers at some point.

I've been experimenting with some gif-recording tools around "Hard CIDER" and I really liked [Gifox](https://gifox.io/).
I'm thinking I might be able to combine it somehow with [keycastr](https://github.com/keycastr/keycastr) for optimal results.
As you can see I'd rather have short animated gifs in the posts instead of real videos, but I might reconsider this down the road.

### nREPL

It has been mostly quiet on the nREPL front lately.
I might end up cutting nREPL 0.7 soon just to get the EDN transport out there and postpone the other planned features for a later release.
There are a few interesting open tickets that are worth taking a look at and could benefit from some help:

* [Generic client notification mechanism](https://github.com/nrepl/nrepl/issues/141)
* [with-safe-transport](https://github.com/nrepl/nrepl/issues/142)
* [Deferred/dynamic middleware](https://github.com/nrepl/nrepl/issues/143)

I'm especially interested in getting nREPL middleware and client authors involved in those conversations.

### Orchard

Work towards the 0.5 release is progressing steadily.
Orchard `0.5.0-beta2` has been out for a while and it seems to be working pretty well.

The planned ClojureScript support might land in 0.5 as well after all.
That's going to be decided soon.

## Ruby

### Ruby Style Guide

After a long period of stagnation I've started to slowly go through the issues in the backlog and tweaked the guide here and there.

I also plan to convert the guide to use AsciiDoc, so we can get richer formatting options and publish it easily in more formats.

### Rails Style Guide

[Andy Waite](https://github.com/andyw8) and [Phil Pirozhkov](https://github.com/pirj) joined the editors team and they have been doing some awesome work cleaning up the guide and updating it.

### RSpec Style Guide

Phil converted the guide to AsciiDoc recently and the [result is really awesome](https://github.com/rubocop-hq/rspec-style-guide).
I'm super excited about converting the Ruby and Rails guides to AsciiDoc as well.

Special thanks to [Dan Allen](https://github.com/mojavelinux) for his work on AsciiDoctor, (kramdown-asciidoc)[https://github.com/asciidoctor/kramdown-asciidoc],
and the valuable tips he gave us during the conversion process!

### Weird Ruby

I've started a new blog series dedicated to some weird and fun aspects of Ruby.
Here are the articles in the series so far:

<ul>
{% for post in site.posts reversed %}
  {% if post.tags contains 'Weird Ruby' %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endif %}  <!-- tags if -->
{% endfor %} <!-- posts for -->
</ul>

I'll be aiming to write 2-3 each month. Fingers crossed.

### RuboCop

RuboCop got two new major releases recently - [0.68](https://github.com/rubocop-hq/rubocop/releases/tag/v0.68.0) and [0.69](https://github.com/rubocop-hq/rubocop/releases/tag/v0.69.0).
The first one was notable for the extraction of performance cops to a separate gem (`rubocop-performance`) and the second one's big highlight is dropping support for Ruby 2.2.

I've also played a bit with the Read the Docs setup for RuboCop and it now serves the documentation for the latest stable version by default.
In addition I've added the documentation for `rubocop-rails` and `rubocop-performance` under the main project's documentation:

* [rubocop-rails](https://docs.rubocop.org/projects/rails/)
* [rubocop-performance](https://docs.rubocop.org/projects/performance/)

I think the result is reasonably good.
I hope we'll soon get some documentation for `rubocop-rspec` as well.

Apart from that - we're steadily moving toward the dream 1.0 release and a new era of stability.
I think it might happen before the end of the year.
The extraction of the Rails cops into a separate gem is the biggest outstanding task.

### Balkan Ruby

[Balkan Ruby](https://balkanruby.com/) is right around the corner and I'm excited about it.
It's really awesome to host such a great event in my humble hometown of Sofia.
Say "Hi!" if you happen to see me there!

## Emacs

I've had very little time for Emacs-related work lately (outside the scope of CIDER and friends).

Still, I found a bit of time to go through some [Solarized](https://github.com/bbatsov/solarized-emacs) and [Zenburn](https://github.com/bbatsov/zenburn-emacs) related tickets and cut the first new
release of Solarized for Emacs in several years.

In other news I've developed a massive time saver when it comes to blogging with Jekyll:

``` emacs-lisp
{% raw %}
(defun jekyll-insert-post-url ()
  (interactive)
  (let* ((files (remove "." (mapcar #'file-name-sans-extension (directory-files "."))))
         (selected-file (completing-read "Select article: " files nil t)))
    (insert (format "{%% post_url %s %%}" selected-file))))
{% endraw %}
```

Linking to other articles has never been easier. Got to love Emacs!

## Misc

All of my major projects (RuboCop, CIDER, Prelude and Projectile) now use [stalebot](https://github.com/apps/stale) to auto-manage stale issues and pull requests. The current policy is the following:

* Issues/PRs are marked as stale after 90 days of no activity.
* They are closed after extra 30 days of no activity.
* Issues marked as "High Priority", "Pinned", "Good First Issue" are never auto-closed.
* Issues with milestones are never auto-closed.

This setup might not be perfect, but I think it makes sense for open-source projects.
We're using this same bot to great effect on my day job, but there the settings are way more aggressive.
I wrote more about the subject [here]({% post_url 2019-05-09-always-be-closing %}).

## In the Real World

I've finished reading "Persepolis Rising" and I'd rate it 3/5. "The Expanse" is certainly
a bloated soap opera at this point. I'll need a lot of willpower to convince myself to make it to the next book
in the series.

I've decided to recover from it with some sci-fi short stories by Philip Dick and William Gibson.
Philip Dick was so ahead of his time! I've always loved his work!

On the cinema/TV front my life has been dominated by "Game of Thrones". Like many of you I've been outraged by how
dark #8.3 was and how the story developed throughout the season in general. I'm fairly certain that whatever ending
George Martin has in store for us is going to be significantly better. I just hope I'll live to read it.

And don't even get me started on "Endgame". It was OK, but it felt like some final victory lap, not like a real movie.
Not to mention it was utterly predictable (unlike GoT). Still, it was an interesting milestone for the Marvel
cinematic universe and I'm curious where does it go from here.

On the side I've finished watching the second season of "Star Trek: Discovery" and it was total garbage.
They really need some better writers there, as the main story was both pretty dumb and pretty boring.
As was everything else in this season...

"The Umbrella Academy" was the next show I've started. So far I mostly like it, although it's definitely
a bit too slow paced for my taste. I love the characters, though, and the general vibe of the show. Reminds me a bit
of classics like "Kick-ass".

Time to wrap it up! Until next time!

[^1]: It used to point to `master`'s documentation by default.
