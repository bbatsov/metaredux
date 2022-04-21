---
layout: post
title: RuboCop Turns 10
date: 2022-04-21 10:07 +0300
tags:
- Ruby
- RuboCop
---

2022 has been [a pretty bad year](https://batsov.com/articles/2022/03/11/the-war-in-ukraine/) for me so far.
Still, even in those hard times one can find things to celebrate.
On this very day 10 years ago I've created [RuboCop](https://rubocop.org), a
linter and formatter for Ruby.

The first step was quite [modest](https://github.com/rubocop/rubocop/commit/afbead34db54506c12a21dbd4ce04fada0f8b9a4). Back then I knew next to nothing about parser, lexer, abstract syntax trees and all that jazz. I recall I planned to implement RuboCop in terms of regular expressions... I remember I had the idea about the project for quite a while, but really struggled to come up with a name that I liked. Picking the name is probably the hardest part of every project and this was certainly true with RuboCop.

The journey that followed it was epic and
beyond anything I could have ever imagined:

- 201 releases
- 800+ contributors
- 220+ million downloads

A project that I've started with very modest goals became extremely popular within
our Ruby community. Impossible is nothing, right? For me RuboCop is the very embodiment of the power of open-source software and open-source communities.

This won't be a long article about the history of the project. I'm way too lazy to write one, plus I already spoke a few times about the (early) history of RuboCop in the past.[^1] This won't be a long article at all. Just a small celebration of RuboCop's 10th anniversary. I don't know about you, but to me 10 years in software is like 50 years in some other industry. Maybe even more.

I'm extremely proud of the RuboCop journey so far and all of our small victories along the way. I'm proud of the community that has formed around RuboCop and of the [RuboCop Core Team](https://docs.rubocop.org/rubocop/about/team.html) members that have been essential to its long term success. I'm proud of all of the lessons that I learned along the way. I'm proud of all the challenges that we overcame together. There were many moments when I was ready to throw in the towel and call it a day. Working on a project of such scale can be extremely emotionally draining.

I know that RuboCop has become a very polarizing project in the community. In surveys it's often near the top in both "the most loved" and "the most hated" Ruby tool categories. Some people have told me that RuboCop played some part in their decision to stop doing Ruby. Admittedly we might have gone overboard at times in our desire to improve Ruby codebases.[^2] We've been accused of destroying some of the fun of programming by imposing too many rules. I guess there's some truth to this.

But we've always listened to our users - a few years ago we promised that with RuboCop 1.0 backward compatibility would become a top priority and we kept our word.
We promised that we'd limit the work that people need to do on upgrades and we kept our word. We always listen and your feedback is always welcome!

Anyways, back to the celebration! To commemorate this glorious occasion we've just released [RuboCop 1.28](https://github.com/rubocop/rubocop/releases/tag/v1.28.0), aka "RuboCop: The Anniversary Edition". It's packed with more
goodies than usual and it has a most conspicuous and round version number.[^3] I just hope we didn't pack more bugs than usual as well, but I guess you'll let us know about this soon enough.

What about the future of RuboCop? Clearly there are always things to improve and we definitely have a few ideas about the journey to RuboCop 2.0:

- Make RuboCop significantly faster (e.g. 2 times faster than it currently is). Think of it as our version of Ruby 3x3.
- Organize cops into some "presets" that would address the common complaint that RuboCop checks for way too many things out of the box. Think of this as something like an "essential", "standard", "community style guide", "github", "rails", "all-in" and so on configuration bundles that enable different cops. It's not like you can't do this today, but probably shipping this out-of-the-box would simplify the setup for many people. There are plenty of interesting ideas to explore in this direction.
- Internal consistency. Make the configuration uniform and more intuitive across the board. I have to admit that in the early days of RuboCop I didn't think much about configuration consistency, which was a major mistake on my part.

I'm confident that regardless of the exact details RuboCop has another bright decade ahead of it. I can only hope that I'll be able to see it through. When I've started the project I was 27 years old, today I'm 37. Time flies really fast. When I was younger I was quite skeptical I'd be able to be an effective programmer after the age of 40, knowing how dynamic our industry is and how things are constantly changing. Still, I love programming just as much as I always did, I love working on open-source projects as much as I always did and I'm cautiously optimistic about powering through another decade of fun OSS adventures. Time will tell.

And that's a wrap! Thanks to all of the people who've contributed to RuboCop over the years in one way or another! Thanks to all the people who used RuboCop and have been championing it. A huge thanks to the members of RuboCop's Core Team - you're all legends as far as I'm concerned and you know it! Thanks to Matz for creating Ruby in the first place! No success happens in a vacuum and there are always a ton of people behind every success story.

> Role models are important.
>
> -- Officer Alex J. Murphy / RoboCop

Those words have always been the driving force behind the [community Ruby style guide](https://rubystyle.guide) and RuboCop itself. I believe that today role models are just as important as they were a decade ago. Here's to another decade of RuboCop being a great role model for Rubyists out there! Cheers!

[^1]: <https://www.youtube.com/watch?v=nrHjVCuVsGA&t>
[^2]: Read this as "there are many cops checking for things that are not very important".
[^3]: At least for software engineers, as 128 is 2^7.
