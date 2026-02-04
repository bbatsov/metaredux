---
layout: post
title: 'Clojure Support in Emacs: One Unnecessary Drama'
date: 2023-09-09 18:09 +0300
tags:
- Clojure
- Emacs
- Drama
---

Over the past couple of weeks I was once again reminded why I've stopped following the infamous `emacs-devel` mailing list a long time ago. A trivial request to include `clojure-ts-mode` in Emacs's NonGNU ELPA package repository[^1], turned into a surprising request by RMS to have built-in support for Clojure in Emacs.[^2]

> It appears that there is no clojure-mode command in core Emacs.
> There is a Clojure mode package, but it is in NonGNU ELPA.
>
> I think that language is important enough that, notwithstanding not
> really being similar to Lisp, we ought to have a major mode to support it.
> Would someone please work on that?
>
> This could be done by getting copyright assignments for code in the
> NonGNU ELPA package, or by writing new code to replace it, or by a
> mixture of the two.

While it's flattering to hear that Clojure is an important language, I'm fairly certain we would have never had this conversation if it wasn't for the submissions of `clojure-mode` and `clojure-ts-mode` to NonGNU ELPA.

So, I was kind of skeptical of this request. Furthermore, I've always been of the opinion that:

- Emacs is already quite bloated and it doesn't make sense to bundle more major modes with it. I can't think of any other editor that bundles so many packages and I'm reasonably sure most Emacs users never use more than 5% of the bundled packages anyways. At this point pretty much everyone is used to installing whatever plugins they need and it seems this works fine.
- Having more things distributed only as packages reduces the maintenance overhead and ensures that the end users will install recent version of the packages.
- The FSF contributor agreement (required for packages that are in Emacs's core or in GNU ELPA) will detract some contributors for either legal or practical reasons (like that you have deal with snail mail for it) - you need to sign if you are to contribute more than 15 lines of code. For me it's super important not to discourage any contributors, especially is a small community like Emacs's or Clojure's.

That's why I've never submitted any packages that I've authored or maintained to either ELPA or Emacs's core.[^3]

Anyways, I've tried to make my points and the Emacs maintainers made theirs. A few ideas where discussed and at some point the conversation took a rather unpleasant turn. First it was suggested that creating a replacement of some sort for `clojure-mode`/`clojure-ts-mode` would be pretty simple. Then it was suggested it could be named `clojure-mode`, because it wasn't cool for third-party packages to use such generic names (even if there are ton of 3rd party modes named in this fashion). It didn't seem like a big deal at the time, though, plus I had some vacation time ahead of me and I dropped this conversation out of my mind.

During my vacation, however, I made the mistake of checking my email and I noticed that a new thread on the topic has started - one discussing a potential barebone major mode that simply derives from `lisp-mode`.[^4] At this point I got a bit upset and tried to understand what exactly was the problem that we were trying to solve here:

> I still haven't heard what are the problems that bringing the Clojure support to ELPA or core would solve. I said I don't buy that installing 3rd party packages is a big obstacle. I've also told you we have a big organization of people behind the clojure-emacs community (and a pretty long track record), so me or Danny are not a bus factor of one. We came and submitted the packages to NonGNU ELPA to make things easier for people who are skeptical of MELPA, etc. From my perspective we've been very accommodating and collaborative, but somehow that's not enough.

As the conversation was getting more heated I tried to summarize my position [here](https://lists.gnu.org/archive/html/emacs-devel/2023-09/msg00224.html) and ask for more constructive tone going forward:

> Instead of having a civilized conversation here, I've felt that it's only "we know better" and to hell with how things used to happen. Sure, you can do whatever you want, but I think that no one will be better off if things in the community happen in this forceful manner.

Then things really blew up and one member of Emacs's team turned this personal with remarks like:

> All I did was to follow up on a suggested path and study technical ways
> to getting Clojure editing support in Emacs, better than what we have
> now, which is 0.  I think is a pretty reasonable thing to do on
> emacs-devel.  It's not an immoral thing to do and I'm not coming for His
> Highness The Great Clojure Authority Life's Work.

I also learned at this point there was 0 Emacs support for Clojure mode. Kind of funny, given that Emacs has been the primary editor for most Clojure developers for the past 15 years, right? I totally disagree that having 3rd party packages for something is equal to 0 support.

I guess this escalation was triggered by a remark I made that it's best to leave such work to people who actually know and use Clojure:

> That's one of the things that bother me the most in the conversations so far - lots of people tell us what the Clojure users need, but other than me and Danny, no one here has any real interest in Clojure. :-) Without an understanding of Clojure and its tooling ecosystem (and it's history) it's hard to make good suggestions about what makes sense and what doesn't.
>
> I already wrote we tried the "thin layer on top of lisp-mode" and this didn't worked out great in the past. Of course, people are welcome to try and learn from experience themselves if they think they can do things better/differently.

I don't think there was something particularly hostile about my message, but the response I got was:

> And no, I don't have to be A Bozhidar-Certified Clojure Programmer to
> want to help out, just as I don't speak 90% of the languages Eglot
> supports, for example and I still try them out for time to time.

Around this time I was also criticized for sharing a blog post and a YouTube presentation on the topic (apparently I was advertising my Clojure street cred or whatever) and for clinging to the control of `clojure-mode` and being afraid of a new Clojure mode.

> If I went by instinct or "the gist" of what you seem to be saying I'd
> say it really sounds like you want to avoid a new Clojure mode in Emacs
> at all costs.  But look, no one is coming for your life's work, really,
> noone is trying to beat you, best you or take away your Clojure street
> cred that you keep boasting about.

Given how much time I've wasted on `clojure-mode` and how little I got for my work on it now I was royally pissed.
I know a wise person would take the high road and ignore this, but I'm not a wise person. You can see my own responses grew progressively more aggressive:

> Another brilliant insight! I really wonder how the f**k you come up with those accusations??? If you think that my self-esteem in life comes from work on Emacs packages - I have to break it to you, but it doesn't. I get "endless satisfaction" from wasting my spare time on random projects for free, so I can be attacked by people like you on mailing lists, right? Hell, yeah!!!
>
> One a less bitter note - I've welcomed Danny's idea to start work on a replacement for clojure-mode and I believe I've been supportive of his work every step of the way (Danny can, of course, confirm this for himself). I've already shared earlier in this message the rough plan for how clojure-mode and clojure-ts-mode should evolve in the next couple of years. I'm juggling more projects that I have time for and the only reason I got involved in clojure-mode is that there was no one else willing to do the work that was required. And you dare accuse me of pumping my ego by clinging to its "ownership"? I can assure that I have a lot more important things in my life.
>
> P.S. You were just boasting about your street cred yourself, which I do find is kind of ironic in a conversation like this one. Are trying to solve problems here or measure our street cred? (or something else?)
>
> Anyways, I can be snarky and aggressive myself. I'm not a particularly calm person and I totally don't tolerate when people try to pin their crazy unsubstantiated claims on me. I'll urge once again to abstain from personal attacks if you expect me to take you seriously.

Not exactly proud of myself here, but that's a classic example that once a conversation starts feeling offensive to some of the participants, it quickly gets downhill.
There's a lot of talk about "toxic" communication these days and for me such threads are a true masterclass in toxicity and gaslighting.

For what is worth at one point I tried to defuse a bit the tension.[^5]

> I was definitely on the defence, given that some of the messages implied things with which I strongly disagree - especially the part that I have something personal to gain from Emacs not having built-in Clojure support. The gist of it for me was simply "I'm wary of the impact that the inclusion would have to the current development process and to the number of outside contributions we'd be getting". I was also unsure of the reasoning for this sudden interest in Clojure from the Emacs team.
>
> Like most people I don't want to do something unless I'm sure it will be valuable in some way (e.g. for the health of the project, for the end users, for Emacs itself, etc). Eli's words that he believes that'd be good for Emacs and I perhaps am not thinking about this got me to pause and think a bit longer. After all, Clojure was probably the biggest reason for new people to try out Emacs around the time it was introduced.
>
> Anyways, despite everything and my own personal frustration, I think I have a reasonably good idea of where we stand right now and what to do next.
>
> P.S. I'm a very emotional person and yesterday I was royally pissed. Normally I think a bit longer about what I write, but that wasn't one of those days.

Alas to no avail. The tone continued to be pretty bad until the very end... This was probably my "favorite" part:

> Let's be clear: your help -- would there have been any -- would of
> course have been welcomed.  But I didn't see it as essential: none of
> your code was involved I was explicitly starting from scratch.  And to
> be frank you didn't even pop in my head when starting the thread, so I
> did was "reply to all".  Anyone can read these emails, after all.  You
> did finally find the thread.  But then all you did was try to
> intimidate, scaremonger and dissuade.
>
> Given that you're now backtracking, err... reviewing your position, we
> might have your Clojure modes in GNU in the end.  So I guess something
> decent might come of those 2 lines of code after all.

So I all did in the discussion was "intimidate, scaremonger and dissuade". I didn't even know I had it in me! :D

So, why am I writing this article? I guess part of the reason is to simply vent out. I'm not fond of bottling up frustration. But the main reason is to show everyone that hostile communication doesn't have positive outcomes. All of this made me quite angry and emotionally drained. I've also seriously started to question my commitment to Emacs after so many years. I'll follow-up on the promises I've made (starting a conversation with the other maintainers of whether they want us to submit anything to Emacs's core), but I'm unlikely to forget what happened. Most likely after the topic is resolved in one way or another I'd sign off from `emacs-devel` forever.

I'd also like to hear your take on all of this? Is there really a (real) problem that will be solved if some Clojure mode is bundled in Emacs? Are you frustrated by the way we've been stewarding the development of `clojure-mode`, CIDER, etc.? Am I the unreasonable person in these exchanges? (always a possibility) What would have done if you were in my shoes?

This dramatic episode reminded me of some ugly/weird moments in the past years like:

- The author of `flycheck` (one of the best Emacs package developers I can think of) quitting Emacs after being frustrated with the communication on `emacs-devel`
- The hostilities between the author of `elgot` and the team behind the competing `lsp-mode`
- RMS randomly dropping that he'd like Emacs to complete with Word
- Hostility against `clang` and improvements specific to "non-free" operating systems

None of them made the Emacs community stronger. In my opinion it was the other way around.
I get that most of the people on `emacs-devel` are true believers in the mission of FSF and the GNU project, but it's very hard to argue with beliefs. There was a time when I was a true believer and nothing would have made me happier than an invitation to submit something to Emacs, but life has made me a lot more pragmatic. These days I care a lot more about great outcomes, regardless of how they were achieved. (e.g. I have no issues with using platforms like GitHub and providing first-class support for Windows and MacOS)

The jury is still out on whether the events that transpired will lead to something useful or not, but right now this feels like a classic case of one unnecessary drama. I hope it's be the last one in which I'll ever participate.

That's all I have for you today. Until next time!

**P.S.** If there's one good thing that came out of this discussion it's that it start a [separate discussion](https://lists.gnu.org/archive/html/emacs-devel/2023-09/msg00011.html) about adding some auto-suggestion of major modes to install. I think that'd be a lot more useful to the majority of Emacs's users than built-in Clojure support.

**P.P.S.** Expect some follow-up regarding the discussions around whether to work towards submitting `clojure-mode` or `clojure-ts-mode` to Emacs. I'll get to this once I wrap up the work on the upcoming CIDER 1.8.

[^1]: See <https://lists.gnu.org/archive/html/emacs-devel/2023-08/msg00395.html>
[^2]: See <https://lists.gnu.org/archive/html/emacs-devel/2023-08/msg00954.html>
[^3]: Outside of the tiny `subr-x` library I've created a long time ago.
[^4]: See <https://lists.gnu.org/archive/html/emacs-devel/2023-08/msg01287.html>
[^5]: See <https://lists.gnu.org/archive/html/emacs-devel/2023-09/msg00337.html>
