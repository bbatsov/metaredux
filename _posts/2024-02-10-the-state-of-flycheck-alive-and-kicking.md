---
layout: post
title: 'The State of Flycheck: Alive and Kicking'
date: 2024-02-10 11:38 +0200
tags:
- Emacs
- Flycheck
---

[Flycheck](https://www.flycheck.org) has been one of my favorite Emacs packages
ever since it was released 10 years ago. I love writing clean idiomatic code, I love the help that lint tools provide with this, and I wasn't particularly happy with the "tried and true" Flymake back in the day. When I came across the Flycheck I was really impressed by its feature set, amazing documentation, very clean codebase and excellent test coverage. All of this was somewhat uncommon for Emacs package circa 2014.

Unfortunately, after [the departure of the original Flycheck author Sebastian Wiesner](https://github.com/flycheck/flycheck/issues/1177), the project lost some of its early momentum. Sebastian was a great Emacs hacker and a great maintainer and it's not easy to fill in his shoes.

Regardless of his departure, however, the project continued to thrive for several years under the maintenance of [Clément Pit-Claudel](https://github.com/cpitclaudel) and `@fmdkdd`(https://github.com/fmdkdd). That's part of the beauty of OSS - a good project will continue regardless of maintainer changes. And as long a project is useful, there will also be someone willing to improve and maintain it.

Lately, however, the current maintainers didn't have much time for Flycheck and this resulted in a pretty big backlog of open issues and pull requests. Some people even asked [whether the project is dead](https://www.reddit.com/r/emacs/comments/sytuv7/is_flycheck_a_dead_project/)...

Well, I'm writing this article to inform all of you that Flycheck is alive and kicking! Recently I've joined the maintenance team myself, as I wanted to help my beloved project grow and evolve. There were a lot of low-hanging fruits and this resulted in making so quick progress early on. So far I've:

- Cut one new release ([33.0](https://github.com/flycheck/flycheck/releases/tag/33.0))
- Merged a couple dozens of PRs and left some feedback on a bunch more, that I hope to merge soon
- Fixed the broken deployment pipeline for the project's website
- Removed a lot of cruft that's no longer needed now that we target Emacs 26+
- Removed the `dash` dependency in favor of using the built-in `seq` library
- Cleaned up the testing and packaging infrastructure of the project

That's just the beginning, of course. I expect that Flycheck 34 will be around sooner rather than later and that it'd be the first Flycheck release that's self-contained (without any third-party dependencies). Down the road I plan to submit the package to NonGNU ELPA and that would certainly help a bit.

What are the plans for the future? I don't have anything grand in mind right now - mostly working through the backlog of issues and PRs. At some point I might rework the project's website, but I found it refreshing to work with RST and Sphinx for a change, so I definitely am not in a hurry on that front. Some items that I've been thinking about:

- Complete transition of the project's tests from ERT to Buttercup
- Update the docs to reflect modern developments (e.g. the changes that happened to our main "competitor" Flymake in Emacs 26)
- Review the list of bundled checkers and trim those that are no longer relevant

If you have any ideas about improvements - feel free to share those in the comments or over at GitHub!

I know that in the Emacs community there are many people who believe that if some feature is bundled in Emacs it doesn't make much sense to develop alternative solutions, but I'm fairly certain that those alternative solutions are driving progress in the long run. E.g. Flycheck likely inspired the big revamp of Flymake, Projectile probably inspired the creation of `project.el` and so on. So having alternatives is a good thing in my book![^1]

Given the massive list of packages that I maintain, I can't promise any miracles, but I can promise you I'll do my best to ensure that Flycheck is in a good shape for many years to come. The power of OSS is you, the community around projects, so I'm hoping that many people will help out on the journey of making Flycheck better. Emacs hackers together strong!

And that's all I have for you today. Keep hacking!

[^1]: Not to mention that for everyone uncomfortable with the Emacs development process it's usually easier to contribute to external packages like Flycheck.
