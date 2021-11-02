---
layout: post
title: Everything is Wrong
date: 2021-11-02 13:24 +0200
tags:
- Clojure
- Code Style
- Meta
---

A few months ago I came across an article named [The "Clojure Style Guide" is simply Wrong](https://roklenarcic.github.io/posts-output/2019-10-15-clojure-style-guide).
It seems the author was very passionate about this subject, as they created a new blog just for this one article and never posted anything else.

The introduction of the article was very amusing for me, as it featured a few words about myself:

> It’s a style guide for Clojure that was created by a man who wrote some of the widely used tooling in Clojure, but primarily he’s a Ruby developer and the author of Rubocop, the most popular Ruby linter.

I've been described in many different ways, but this one is probably my favorite so far. For the record - I never viewed myself as a Java developer, a Ruby developer, a Clojure developer or whatever. I happen to do some things for a living, and I happen to enjoy doing some other things outside of my work. Yeah, Clojure has always been just a hobby for me, but I hope this doesn't diminish me as a Clojure programmer in the eyes of my fellow Clojurians.[^1]

Back to that blog post. Despite its ambitious name it focuses only on the following 2 (!!!) points from the guide:

* The use of [semantic indentation]({% post_url 2020-12-06-semantic-clojure-formatting %})
* 80 characters line limit

I definitely didn't see this coming. It's hard to find better examples to illustrate what bike-shedding is all about, as those guidelines are certainly not the most important part of the style guide. I've worked on several style guides
over the years and always the longest, most heated and least constructive conversations revolve around the most trivial and unimportant topics. Human nature I guess.[^2]

The Clojure Style Guide starts with the following "disclaimer":

> A style guide is about consistency. Consistency with this style guide is important. Consistency within a project is more important. Consistency within one class or method is the most important.
>
> However, know when to be inconsistent — sometimes style guide recommendations just aren’t applicable. When in doubt, use your best judgment. Look at other examples and decide what looks best. And don’t hesitate to ask!
>
> In particular: do not break backwards compatibility just to comply with this guide!

A style guide is there to spare you the need to make lots of trivial decisions and
to engage in an epic number of bike-shedding conversations. It is not, however, a replacement for
doing some thinking and applying your common sense/best judgment.

Anyways, I'm writing this article mostly, because the "everything is wrong" post
is just one representation of a common problem in today's world. People love to
argue about pointless topics. People prefer to go straight for rants without even
trying to engage in some form of conversation beforehand. They also prefer to
promote their personal perspective as an absolute truth, even if it's super subjective and (very often) not really
aligned with reality. Here's an example:

> This is very upsetting, because I don’t think there’s ever been an agreement about the Clojure style, yet this one document made by this one person somehow became the golden standard.
>
> There was no examination of what is suggested, it was all just accepted. It’s even named Clojure Style Guide like it’s an official thing, an ISO standard or a RFC and not just an opinion of one person.

For the record - there have been numerous conversations on the project's [issue tracker](https://github.com/bbatsov/clojure-style-guide) and over 50 people have
contributed directly to the current content of the guide. Not a great number for sure, but definitely not the work of a single person either. I think you'll find [the sources of inspiration](https://guide.clojure.style/#sources-of-inspiration) compelling, and
the [editor team](https://guide.clojure.style/#editor-team) trustworthy. I may have started the project, but I consider myself nothing more than a mere editor and chronicler
of the wisdom of our great community at this point. And yeah - I do believe you can achieve great results without going through the pain of an ISO standardization or an RFC. Anyone remember
the impact of the ANSI Common Lisp standard on the evolution of Common Lisp? In case you don't - it almost killed the language, as updating the damn standard is way too hard and expensive.

But that's besides the point. Everything is wrong all the time. Everyone is wrong all the time. That's the world in which we live in.
It's a tiring, frustrating and simply demotivating much of the time. And it's on all of us to make an effort and do something different and maybe even more constructive for a change.
A community is defined by the interactions that take place in it. A great community is defined by strong, respectful and constructive interactions. In such a community something may occasionally be right. Over and out.

[^1]: If someone wanted to embarrass me properly, they should have pointed out that I haven't been programming professionally for the past 10 years (I've held mostly managerial roles in this period, that required very little programming on my part). I guess I'm not even a real programmer anymore.
[^2]: I tremble when someone asks me "What you do you think about single vs double-quoted strings in Ruby?". People expect me to have some strong position on the topic, but frankly I couldn't care less.
