---
layout: post
title: Projectile Turns 10
date: 2021-10-28 09:13 +0300
tags:
- Emacs
- Meta
---

I just realized this morning that on the 31st of July 2021, my very first
open-source project[^1] [Projectile](https://github.com/bbatsov/projectile) (a
project interaction library for Emacs) had turned 10 years old. Time sure flies
fast!

I still remember the humble beginnings of the project, which started out simply
because I was frustrated that the venerable `find-file-in-project` didn't work
on Windows back in 2011. I had some Linux driver issues with my brand new ThinkPad, which
forced me to temporarily work on Windows and the rest is history.[^2]

Using Windows was an option, but not using Emacs was not an option. As most
packages that deal with project interaction assumed an Unix-like environment and
shelled out to Unix command-line tools (e.g. `find`, `grep`, etc), I decided to
build a tool that's truly portable and won't rely on any external commands.
Over the course of the years Projectile moved away from this initial vision
(mostly for performance reasons), but it still remains one of very few tools
that will work on Windows without installing any Unix compatibility packages.

A lot of unforeseen things happened over the course of the years:

* I never thought that anyone but me was going to
use Projectile, but it became quite popular over time and before the addition of a built-in `project.el` package to Emacs, it was
certainly the most popular tool in this area.
* The scope of the project grew beyond my wildest dreams to cover a lot of things I never even thought of.
* The simple decision to show a dynamically updated project name in the modeline resulted in years of pain for people using Projectile over TRAMP. Frankly, before
Projectile I had no idea how many people were still using it! I'm pretty sure a lot of people hated me bit time over the decision, as it took me way too much time to address the problem.
* Projectile spawned an ecosystem of over 20 plugins (e.g. `helm-projectile`, `projectile-rails`, `consult-projectile`).
* The main package reached over 2 million download on MELPA!
* I learned a lot about how to run a open-source project, while running into every possible problem that one can imagine.
* I rejected an opportunity to submit Projectile to GNU ELPA and potentially to Emacs itself early on. Long story short - I value a low bar to contribution for my projects more than a wide reach. Call me an idealist.[^3]
* I got inspired to work on many other open-source projects.

I'm reasonably sure that without Projectile, I would have never created my latter and better known open-source projects RuboCop and CIDER.
Every great adventure has a beginning and for me that beginning was certainly Projectile.

So, what has the future in store for Projectile? Does the project even have a future?

It's still one of my favorite projects and it's still the one I use
all the time. Frankly speaking, we reached the point where Projectile covered all of my own use cases extremely well a long time ago and
afterwards I wasn't spending that much time working on it. From time to time projects are more or less done and no additional functionality
is actually needed. In recent years the focus has been mostly bug-fixes, performance improvements, better documentation, etc., and it's
likely going to remain the same for the foreseeable future.

I've read that now that Emacs has the built-in `project.el` package a lot of people
have stopped using Projectile and think that the project is irrelevant, and I respect their opinion. Projectile never aimed for
any form of world domination, and it's obviously hard to compete with a built-in package, as it has a home-field advantage you can never match.
Still, I think that competition is not a bad thing, and having a project with a lower bar to entry (when it comes to contribution) has its merits
as well. I'll write more on this subject down the line.

I don't really know what the future holds, but I'm fairly certain that as long as I can wield a keyboard Projectile will be alive and well.
And the magic of open-source might ensure that remains the case even with me out of the picture.
Thanks to everyone who has contributed to the project, supported it, criticized it, or simply used it over the past decade! Projectile wouldn't be here without you,
and I wouldn't have learned the valuable lessons that I learned in process of developing and maintaining it. Projectile will forever have a special place in
my heart! Here's to the next decade and whatever new adventures and challenges it may hold! Cheers!

[^1]: I did start Emacs Prelude in the same year, so it's a bit hard to say which one was truly first. In my mind, however, it has always been Projectile.
[^2]: That also prompted me to write my [(in)famous rant about the poor state of the Linux desktop experience](https://batsov.com/articles/2011/06/11/linux-desktop-experience-killing-linux-on-the-desktop/).
[^3]: By the way - these days Projectile is available on the new NonGNU ELPA package repository, that's going to be enabled by default in Emacs 28.
