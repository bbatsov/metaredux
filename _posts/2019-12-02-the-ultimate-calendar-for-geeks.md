---
layout: post
title: The Ultimate Calendar for Geeks
date: 2019-12-02 16:57 +0200
categories: posts
tags:
- Meta Advent 2019
- Command-line Tools
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

You know what's the most important thing during the Advent? You're totally right - that's having
a good calendar where you can constantly check the time left until Christmas.
Today we've got all sorts of fancy calendars - GUI apps, web apps, mobile apps... I hate them all![^1]
Perhaps I'm just a bitter man stuck in the past, but I really prefer to interact with
simpler and more hacker-friendly calendars. My favourite calendars are one that comes with Emacs[^2] and
the command-line `cal/ncal` tool.

<!--more-->

If I'm in Emacs and I need the calendar I'd just do `M-x calendar` and that's it.
The rest of the time[^3] I'd just summon quickly my terminal with `C-~`[^4] and invoke `cal` there.
As Emacs hasn't achieved world dominance yet, I'll focus on `cal` for the remainder of this article.
`cal` looks like this:

![cal.png](/assets/images/cal.png)

`cal` is quite flexible and here I'll share my favourite ways of using it:

* Show the previous and the following months - `cal -3`
* Show the entire calendar for the current year - `cal -y`
* Show the entire calendar for some year - `cal -y 2020` (the `-y` is optional)
* Show some specific month - `cal -m 5`. You can prefix the month with `f` or `p` for `following`/`previous`. Useful if you
want to check some month in an adjacent year.

Now let's see `cal` in action!

![cal_demo.gif](/assets/images/cal_demo.gif)

The `ncal` command is pretty similar, but displays the calendar a bit differently and has flags to show
the date of Easter (amongst other features):

```console
$ ncal -e 2020 # Catholic Easter
April 12 2020
$ ncal -o 2020 # Orthodox Easter
April 19 2020
```

Generally I use `cal` mostly because I'm too lazy to type one extra character or alias `cal` to `ncal`.
You can learn more about the two commands [here](https://www.howtoforge.com/linux-cal-ncal-command/).

Time to wrap it up for today. I hope this post will make it easier for you to
keep track of the days until Christmas and that it showed you a bit of the
beauty, simplicity and elegance of command-line tools. See you soon!

[^1]: Especially the built-in macOS calendar app.
[^2]: You're shocked, right?
[^3]: Almost never.
[^4]: Also known as the best keybinding for drop-down terminals ever!
