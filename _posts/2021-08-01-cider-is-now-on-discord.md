---
layout: post
title: CIDER is Now on Discord
date: 2021-08-01 08:30 +0300
tags:
- Clojure
- CIDER
- Discord
---

Over the past few months I've been trying to find better ways to support the community around CIDER (and my other bigger OSS projects), that's
why I was more focused on documentation and exploring new communication channels, instead of new features and fixes.
Over the years I've tried a lot of different support channels with varying degrees of success:

- Mailing List (Google Groups) - never gained any traction. I've noticed the Clojure mailing list hasn't been that active in recent years as well, so I think mailing lists are losing popularity in general. A pity, if you ask me. At any rate - given that no one uses the mailing list I definitely considering to shut it down completely.
- Gitter chat - never gained any traction. Gitter was somewhat popular a few years ago, but that's no longer the case.
- GitHub Discussions - it's still new, but there's not much traction there.
- StackOverflow - never gained much traction either, although it's certainly more used for CIDER than any of the above options.
- Slack (Clojurians) - the `#cider`, `#emacs` and `#nrepl` channels there are quite active. CIDER's channel alone has over 1700 users, which probably makes it one of most popular channels on Clojurians. Seems that Slack was a big success for Clojure in general, and for CIDER
in particular.

A lot of support also happens in GitHub issues, but I consider this accidental rather than intended and now I can easily convert
all issues that should have been discussions into discussions.

So, it seems that Slack is the solution for support/discussions/etc, right? It's big, it's active, so it must be great, right? Unfortunately, I don't think that's exactly the case. Sure, it has a lot of users, but
it's really painful for me to use Slack because of several things:

- The logs disappear fairly quickly on the free plans that the Clojurians Slack is using. Often by the time I see something, all the context for it has disappeared. I know the logs are stored somewhere else, but I don't really want to have to dig for them. I prefer them in chat.
- There's not enough granularity in the conversations as they happen in just a couple of channels and support requests, feature requests, etc quickly get
intertwined. I could have created more channels like `cider-support`, `cider-beginners`, etc, but experience with Slack has taught me that most people probably
won't bother to use them (unless constantly prodded by someone). And Slack's threads are notoriously easy to miss. In our company we had even forbidden them for a while, as so many people hated how they were implemented.
- Slack has been betting progressing more enterprise and less hackerish every year. I still remember it when it was hip, simple and cool. Now it's something very different.
- It uses a ton of RAM.

All this means that I don't really enjoy using Slack much. Frankly, the only reason that I still frequent the Clojurians Slack is that I'm using Slack for work anyways. I've been pondering about alternatives of Slack for a while now, but I couldn't find something compelling. Until I did.
At the end of last year I stumbled upon Discord, the famous chat app for gamers, and I was shocked to realize that it's a pretty general purpose chat app, that fits my needs way better than Slack:

- It's simpler (less features, less hassle). It actually reminds me of the early days of Slack in some ways.
- It uses way less resources.
- The history doesn't disappear on the free plan. That's big!
- I can run my own Discord "server" where I get to define the channel structure and how channels can be used. Probably not very important for you, but quite important for me.
- It does threads better.

I know that some people don't like Discord for various reasons (mostly related to some privacy fiasco in the past), but from my perspective it's better than Slack in every way (at least for my CIDER/OSS projects use-case).

[CIDER's Discord server](https://discord.gg/32qfjATb3Q) has been up for a few months and has the following channel structure:

- `#general`
- `#support`
- `#ideas`
- `#nrepl`
- `#orchard`
- `#clojure-mode`
- `#clj-refactor`
- `#inf-clojure`

A bunch of CIDER-related channels (the first 3), plus a few extra channels for my CIDER-related projects. Pretty simple, and super focused, at least from my perspective.

The Discord server is not particularly active yet (it has only a 110 users), but I haven't advertised it much, so I'm optimistic about the future. My plan is to gradually reduce my presence in Slack and
focus entirely on Discord. It'd be cool if more people adopted Discord (not just for CIDER, there's a pretty big Clojure community on Discord in general),
but I totally understand everyone who's happy with Slack and wants to stay there. I have a feeling that part of its popularity is coming from the fact that so many companies are using Slack today as the official company chat, so adding one extra workspace there is not much hassle for many people. Given the size of CIDER's Slack community, I think it's going to continue
being helpful even without me there. You can join CIDER's Discord server [here](https://discord.gg/32qfjATb3Q) and decide for yourself if it's better than Slack.

That's all I have for you today. Keep chatting!
