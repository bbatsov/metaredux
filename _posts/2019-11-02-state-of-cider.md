---
layout: post
title: 'State of CIDER Survey (2019)'
categories: posts
tags:
- Clojure
- Emacs
- CIDER
- Survey
---

One of the hardest things for any project author is to figure out whether their
perspective on a project is aligned with perspective of the project's
users. Henry Ford supposedly knew better than his customers what they needed,
but if that's true I'd say he was a rather special case. I'm often guessing what
people need and would find useful, what their issues are and what tools are they
using.  From time to time I guess right, but often I guess wrong.  A couple of
notable examples immediately come to my mind.

I always assumed that people didn't care much about the minimum supported
Emacs version, as it's pretty easy to upgrade Emacs, so I was dropping support
for older Emacsen quite aggressively. Last year, however, I got some feedback
after my "Clojurists Together" funding round that people who were working
in corporate environments were really suffering, because they couldn't upgrade
the software on their machines. I was enlightened.

I always assumed that people don't care much about changing the keybindings of some commands
and I was doing this quite a lot in the early days of CIDER. Now I know better...

Similarly when I introduced `cider-nrepl` many years ago I thought that having
people set it up manually won't be a big deal, and I grossly underestimated how
complex this task was for many people. Fun trivia - automating
this setup in CIDER 0.10 was one of the most celebrated changes ever.

At some point I foolishly believed that everyone read CIDER's manual before starting to use it.
If only that were true![^1]

I've always loved the concept of surveys like "State of Clojure" and the insight
they bring. I've meant to do something similar for CIDER for quite a while now -
probably for at least 5 years. For one reason or another, however, this task
just kept sitting in my backlog. Until today...[^2]

Please, allow to me to introduce to you the first ever [State of CIDER
survey](https://forms.gle/TJMVMEgUrNew1Cf8A)!  It's far from perfect, but it's a
start. I'd really like to connect with more of you - the people who are actually
using CIDER. I want to understand what's the environment you're working in, I
want to learn what's your background and how are you using CIDER. I want to
learn what do you like and what do you hate. Long story short - I want to align
the things I'm working on with your needs.

I also want to get a feeling for how many people would be consider supporting
the project financially down the road.  I'm hoping to inspire a few more
companies and individuals to pitch in to our
[OpenCollective](https://opencollective.com/cider) and [GitHub
Sponsors](https://github.com/sponsors/bbatsov) campaigns. I'm a dreamer, but I'm
not the only one... The subject of the economics of OSS development is something I plan
to discuss at length in a dedicated article.

The survey will be open until the end of next week. Looking forward to your responses!

[^1]: Not that I ever read any manual before diving into something.
[^2]: All the credit here goes to the "Clojurists Together". Having goals to work towards makes a big difference!
