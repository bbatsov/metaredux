---
layout: post
title: Using Discord for OSS Projects
tags:
- Project Maintenance
- Discord
date: 2021-10-23 14:34 +0300
---
You might have noticed that this year I've been promoting Discord
as the main chat for my bigger OSS projects like RuboCop and CIDER.
You may have been surprised by this move, as Discord originated
in the gaming community and is still mostly associated with it.
That's why I decided to explain my reasoning in this short article.

I discovered Discord at the beginning of the year, and I like most people
I assumed it was just some chat for gamers.[^1] However, after playing with it
for a few months I figured out it's a great fit for the needs of my projects
for several reasons:

* You can create new Discord servers[^2] for each project, where you can create a nice channels structure like `#support`, `#hacking`, `#general`, `#feature-requests`, etc.
* The UI is simple and clean, and the client application is not a resource hog (unlike Slack). The UI assessment is obviously subjective, but a lot of people I've spoken with on the subject voiced the same opinion.
* Even on a free plan you have unlimited chat history retention. That's one of my main
issues with Slack and the famous [Clojurians Slack](https://clojurians.slack.com/), often the history there
disappears before I've had the chance to read something.
* Discord's voice channels are a nice way to quickly speak with someone on some topic. Admitted I use those mostly while gaming, but I definitely see the potential for
doing something like "office hours" with your community or whatever.
* I like Discord's version of threads a lot more that Slack's. Slack threads are just too easy to miss.
* Discord has very nice moderation tools.
* It's a cross-platform tool. Although I guess most of the famous chat apps are cross-platform apps these days.
* It's not Slack.

Discord reminds me a lot of the early days of Slack, where it was clear the target
audience where hackers (compared to big enterprises today) and the whole UX was
optimized for this. Compare this to all the buttons and menus that have replaced the old
days of markup and text commands.

Obviously Discord is not perfect (which tool is?), but I like it a lot and I've decided to place my bets on it. I'm well aware that it's a proprietary tool, and that there were some privacy concerns about it in the past, but that's fine with me, given the lack of good (in my subjective perspective) truly free alternatives. I never forget that perfect is the enemy of done.

There's also the meta matter that chat never really took off for any of my projects, expect
CIDER's Slack channel on the Clojurians Slack (`#cider`). I guess the majority of people simply
prefer to use only GitHub for project-related conversations and that's perfectly fine for me. If I weren't using Discord for other purposes, I doubt I would have bothered installing it just to chat about my projects. Same with Slack, really - the only reason I'm the Clojurians Slack is the fact that I use Slack for work.
I guess that's also the reason why Gitter never took off, even if it was designed to be a chat for OSS projects - nobody was using outside of OSS, which really
limited its appeal.

Anyways, that's all from me for today. I just wanted to encourage people looking to
add some chat to their OSS projects to consider Discord. I'm also curious to hear what chats
are you using to collaborate on OSS projects if any. Feel free to share your thoughts in the comments.

[^1]: Truth be told, I kept confusing Discord with Discourse.
[^2]: You can think of Discord servers as an instance of Discord or an isolated namespace. They are basically the same as workspaces in Slack.
