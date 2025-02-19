---
layout: post
title: RuboCop and Git
date: 2019-12-19 12:23 +020
categories: posts
tags:
- Ruby
- RuboCop
- Meta Advent 2019
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

From time to time we'd get a request to add support to RuboCop for
checking only git diffs or only files that were changed. All such
requests get rejected for a simple reason - simplicity. While it
might be convenient for end users to have their lint tool know about their
VCS, this adds a lot of complexity. Not to mention that git is not the only
VCS around and if we support it down the road people might start requesting
adding support for another one. Sure, today it seems that almost everyone's
on git, but this was the case about `cvs` 20 years ago and about `svn` 10 years ago.
Nothing lasts forever.

I recall at some point I was convinced to add some basic git integration[^1] and
we ended up reverting it, as it caused some unforeseen problems[^2]. Simple
changes often turn out to be tricky. Today I can assure you that while I'm the
steward of the project it will never feature any form of integration with any
VCS. It's just too much pain and it's completely orthogonal analyzing source
code.

Still, I understand the perspective of the people who need something like this,
and in today's article I'd like to give you a few tips on how to integrate RuboCop
and git to some extent.

The simplest option is just to run RuboCop on all staged files:

```console
$ git diff --name-only --diff-filter=MA | xargs rubocop

# This also works
$ rubocop $(git diff --name-only --diff-filter=MA)
```

RuboCop will simply ignore any non-Ruby files, so you don't have filter out anything.
Seems like a great pre-commit hook to me.

You can obviously do something way fancier if you want to. Here's how you can check
all files that are not merged in `master` yet:

```console
$ { git diff HEAD --name-only --diff-filter=MA & git diff origin/master..HEAD --name-only --diff-filter=MA; } | sort | uniq
```

That's quite useful if you're working on a branch with multiple commits.

The point I'm trying to make is that it's really simple to feed RuboCop a list of files you need to check.
It's totally up to you to figure out what this list is, though.
The situation is similar with portions of files, as you can just feed RuboCop some Ruby code directly via the standard input.
Still, you'll have to make sure that those regions of code are something parsable, which can be tricky.

The abundance of third-party tools bridging the gap between RuboCop and git seem to support my theory. Here's a few
notable examples:

* [pronto-rubocop](https://rubygems.org/gems/pronto-rubocop)
* [rubocop-git](https://rubygems.org/gems/rubocop-git)
* [rubocop-select](https://rubygems.org/gems/rubocop-select)

I haven't used any of them, but they all seem pretty popular, so I guess they get the job done.

You'll also find numerous blog posts and gists online where people describe their personal sophisticated
integration strategies. They are full of interesting ideas, but are usually too complex for my
taste. Not to mention for my passion for focused efforts on code style that quickly normalize
the project and eliminate the need for anything more than a trivial pre-commit hook for changed files.

Generally, I'd advise against any approach that fixes problems only within parts of a file.
This never made sense to me and it never will. I've always been a big believer that
every file should have uniform code style, so the very least you can do is to fix all the
problems and inconsistencies in the files that you touch in your day to day work. If
you push yourself to do this at the project level that'd be even better.

That's all I have for you today. See you again tomorrow!

[^1]: <https://github.com/rubocop-hq/rubocop/pull/6546/>
[^2]: <https://github.com/rubocop-hq/rubocop/issues/6625>
