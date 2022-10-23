---
layout: post
title: Back to Linux
date: 2021-07-31 12:38 +0300
tags:
- Linux
- macOS
- Windows
---
## Background

**Note:** _I've started writing this article about one year ago (September 2020), but I dropped it at some point.
Its final version is way less ambitious than my original plans for it, mostly because I forgot some of things that were
on mind back then. Still, better than nothing._

A long time ago (in 2011) I [wrote about my frustrations with Linux](https://batsov.com/articles/2011/06/11/linux-desktop-experience-killing-linux-on-the-desktop/) that led me to
abandon the OS after having spent quite a lot of time on it.

After this article I made one failed attempt to convert to Windows and eventually
I settled on macOS for almost a decade. While I was reasonably happy with macOS
for most of the time, it never felt like home and I kept longing to revisit Linux.
I was also missing having a custom-built desktop PC for a while, so I was very
tempted to get one and run Linux on it. For various reasons (e.g. a lot of work-related
travel and excessive laziness) I never got to doing so. Until now.

2020 has been a horrible year on many levels, but it presented me with an unique
opportunity to follow through with my Linux PC plans, as I was finally grounded
at home and had some extra time on my hands. In this article I'll share my
experience revisiting Linux and rediscovering the joy of being in control of
your operating system and your hardware.

## The Downfall of macOS for Software Development

I won't go into much details here, as I hope everyone has noticed the shift in macOS to be
something more like iOS. That's good for most users, but not for software engineers.

Apple used to spend a lot of time courting developers to their OS some 15 years ago, but now they
only seem to care about macOS and iOS devs. They rarely update the Unix tools that ship with macOS,
they still haven't created a package manager (everyone's basically using `homebrew`), etc.

At this point it's clear to me that macOS is not meant to be an OS for tinkerers, but rather for consumers.
That's not a bad thing, it just doesn't sit very well with me.

On a more practical note, here are a few things that have bothered me with macOS in recent years:

- Pushing aggressively for the use of the App Store, which limits what applications can do. The OS tries hard to dissuade you from using any externally procured applications.
- Bringing some applications and weird features from iOS to macOS (has anyone ever used the launchpad?).
- The need to reinstall XCode after each macOS upgrade.
- Almost every major OS upgrade breaks Homebrew or some tool that I use (like Karabiner, a keyboard remapping utility).
- macOS ships by default with Emacs 22. That's not a typo. As noted above there are plenty of outdated Unix tools that ship with macOS.
- Cheap tricks to obscure some problems, like hiding the remaining battery life estimation.

I can go on, but I'll stop at this point. I hope you get the idea.

## The New Hardware

Here's the hardware that I got in early July 2020 (it took me almost 3 months to settle on all the parts):

| CPU | AMD RYZEN 7 3700X |
| GPU | ASUS DUAL Radeon RX 5500 XT EVO OC 8G |
| Motherboard | AORUS X570 ULTRA |
| SSD | 960GB M.2 2280 Corsair Force Series MP510 |
| RAM | 32GB (2x16GB) DDR4 3200MHz Corsair Vengeance LPX |
| Case | Fractal Design Define 7 |
| PSU | Fractal Design Ion+ 660W Platinum |

As you can see there's nothing fancy about it. I wanted to build a decent
workstation, not a gaming rig. Still, I opted to get a decent discrete GPU, as
there were a few PC games that I was hoping to eventually play (StarCraft II,
Tomb Raider, Diablo III, etc). I went for the Radeon mostly because of the AMD's
(supposedly great) open-source GPU driver for Linux. In hindsight this was a big mistake, but I'll
expand on this topic later.

**Note:** I knew that an Intel CPU with built-in GPU was the safest option in
terms of driver support, but I really wanted a Ryzen CPU and a reasonably
powerful discreet GPU.

Here's my desktop before and after assembly:

![desktop_2020_before.jpg](/assets/images/desktop_2020_before.jpg)
![desktop_2020_after.jpg](/assets/images/desktop_2020_after.jpg)

I have to say that this 1,600 EUR desktop completely blew out of the water my way more expensive MacBook Pro. Of course,
desktops always offered better value/performance than most laptops, but in recent years we've all become completely obsessed with laptop/tablets.
While portability is definitely a problem with desktops, they also have some cool advantages like:

- They don't overheat
- They don't run out of juice
- You can actually fix/upgrade them
- They force you to work on a desk (meaning the ergonomics are usually better)

End of this slight lyrical detour on the merits of ancient technology from more sophisticated times.

## The Distro

I decided to play it safe and I went with Ubuntu 20.04, which was the latest release at the time.
Historically I preferred Arch Linux and Fedora, but I wanted to minimize the setup efforts.

When I was younger I loved spending lots of time to tweak every aspect of my
setup (e.g. building a custom kernel, tuning the compilation flags for my
packages, obtaining proprietary drivers from third-party repos, etc), but now I'm older, lazier and more practical. I still enjoy
tinkering with my setup a lot, but I don't want to be forced to tinker (the
Gentoo way).

One general trend I've observed is that Linux distros are now more complex than they used to be. Life
seemed pretty good and simple in the days of System V init, but today systemd dominates everything and makes
it harder to comprehend. At least to someone like me, who completely missed the transition to systemd.

> systemd is a software suite that provides an array of system components for Linux operating systems. Its main aim is to unify service configuration and behavior across Linux distributions; systemd's primary component is a "system and service manager"—an init system used to bootstrap user space and manage user processes. It also provides replacements for various daemons and utilities, including device management, login management, network connection management, and event logging.

So much for the single-responsibility principle.

## The Drivers

Everything worked out of the box, but unfortunately not everything worked
well. I was getting some intermittent issues with my wi-fi and X.org kept
crashing due to some issues with the GPU drivers. A quick Google search
revealed I wasn't the only person facing these [GPU issues](https://gitlab.freedesktop.org/drm/amd/-/issues/1149).

No matter what I tried (and I
tried a lot of things - updated kernels, Mesa, random patches I found here and
there, etc) I couldn't solve the GPU issue and my computer kept crashing 1-2 times
per day. As you can imagine this was quite frustrating. Funny enough, after each change the
crashes would get more frequent or less frequent. Never completely disappear, though.

The wi-fi problem was much easier to address - I just switched to ethernet and never bothered
to investigate it.

In the past I also struggled with getting my printer to work properly, but I no longer have a printer, so
that's one of those problems that solved itself.

Hardware support was the main reason I left Linux a decade ago and sadly the situation there
hasn't improved much. Perhaps the situation is better with Nvidia GPUs, but I was hoping
avoid dealing with a proprietary driver.[^1]

## The Apps

Little has changed in the past 10 years, not to mention I don't use that many apps - mostly a browser (Chrome and Firefox), Emacs and a terminal (Guake).
In short - everything was better than I remembered it, but nothing was fantastic. GNOME and its apps (e.g. Nautilus) still have plenty of rough edges, but they get the job done.

[Albert](https://github.com/albertlauncher/albert) was a great alternative to Alfred (one of my favorite macOS tools). That's probably the only new app that I used this time around.
Some proprietary apps that I need for work (like Zoom, Slack and Dropbox) didn't work/look great (especially when compared to their macOS/Windows versions), but this is what I expected anyways. Still,
they were working better than I remembered them to. Given the small market share that desktop Linux has, I never expected big corporations to start
investing heavily in Linux support. I recall that 5 years ago Zoom kept crashing constantly on Linux (colleagues using Linux were often dropping out of
Zoom calls, that's how I know this), but at least it was working reliably for me.

Back in the day I complained about how bad Skype for Linux was, but fortunately I no longer have to use Skype, so this is a problem that solved itself.
I also complained about the quality of OpenOffice - another application I no longer needed, as I'm using mostly Google Docs these days.
In general the trend to move from native desktop applications to web based applications is definitely a benefit for Linux users. In some cases I actually
preferred the web versions of applications that have a Linux desktop version as they seemed to work better to me (e.g. Slack and Spotify).
Skype also has a nice web app these days, if you happen to need it.

One more thing - I was happy to see that there were a lot of games (by Linux standards) in Steam, despite the failure of Valve's Steam Machine project
(a gaming console running Linux). I tried out a couple of older games and they worked fine, apart from the video crashes I was getting because of the
GPU driver issues.

## The Development Experience

Absolutely divine! Proper package manager (`apt`). Proper service management (with `systemctl`). Emacs in all its GTK glory! All the fantastic GNU utils. Docker at its home. No more XCode installations. It doesn't get better than this!

I'm 100% certain that Linux offers the best development experience for most software engineers. When there aren't hardware issues
to affect it negatively, that is.

## Epilogue

The title "Back to Linux" is a bit of a misnomer, because of the long period in which this article
stayed on my backburner. By the time I wrapped it, I'm no longer using Linux, at least not
natively.

Due to the GPU driver issues, in the end I've opted to switch to Windows 10 and WSL
(basically a Linux VM running in Windows). One year later I'm still a happy
camper.  With Windows 10 I got the best of both worlds:

- no driver issues
- proper Linux development experience

As a bonus - I also got the opportunity to play a lot more games.[^2]

Windows still has its quirks, but the experience with it certainly beats the
experience I had with Linux. All proprietary apps work great, the drivers are
solid and I'm essentially working in Linux all of the time. The only thing from
macOS I'm really missing is the nice collaboration/sync between all Apple
devices - e.g. you take a picture on your phone and it immediately goes to your
computer. You get an SMS on your phone and it's also on your computer. Not a big
deal, but this walled Apple garden kept me on macOS even though I was getting
more and more frustrated with it. I hate walled gardens!

In short, hardware support remains the biggest weakness of Linux as desktop
operating system, just as always. It's super disappointing that even after
carefully choosing hardware for Linux compatibility I still ended up with a GPU
that didn't work reliably. I'm reasonably sure that if I had opted for an older GPU
it would have worked better, but I didn't really want to make too many compromises.
Who knows, probably by now the problems that plagued me have been fixed, although I'm
not sure eager to check this.

On the bright side - it was a ton of fun to assemble a new PC, after all those
years. You should have seen how happy I was when the thing booted from the first
try! And it was nice to check in on the progress made in the world of Linux and
to find a reason to give Windows another chance.

That's all I have for you today. Keep hacking!

[^1]: And I happen to be a huge AMD fan.
[^2]: Not sure if that's really a bonus though.
