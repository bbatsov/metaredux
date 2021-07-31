---
layout: post
title: Back to Linux
tags:
- Linux
- macOS
- Windows
date: 2021-07-31 12:38 +0300
---
## Background

**Note:** I've started writing this article about one year ago (September 2020), but I dropped it at some point.
Its final version is way less ambitious than my original plans for it. Still, better than nothing.

A long time ago I [wrote about my frustrations with Linux](https://batsov.com/articles/2011/06/11/linux-desktop-experience-killing-linux-on-the-desktop/) that led me to
abandon the OS after having spent quite a lot of time on it.

After this article I made one failed attempt to convert to Windows and eventually
I settled on macOS for almost a decade. While I was reasonably happy with macOS
for most of the time, it never felt like home and I kept longing to revisit Linux.
I was also missing having a custom-built desktop PC for a while, so I was very
tempted to get one and run Linux on it. For various reasons (e.g. a lot of work-related
travel and excessive laziness) I never got to doing so. Until now.

2020 has been a horrible year on many levels, but it presented me with an unique opportunity
to follow through with my plans, as I was finally grounded at home. In this article I'll
share my experience revisiting Linux and rediscovering the joy of being in control of your
operating system and your hardware.

## The Downfall of macOS

I won't go into much details here, as I hope everyone has noticed the shift in macOS to be
something more like iOS. That's good for most users, but not really for software engineers.

Apple used to spend a lot of time courting developers to their OS some 15 years ago, but now they
only seem to care about macOS and iOS devs. They rarely update the Unix tools that ship with macOS,
they still haven't created a package manager (everyone's basically using `homebrew`), etc.

At this point it's clear to me that macOS is not meant to be an OS for tinkerers, but rather for consumers.
That's not a bad thing, it just doesn't sit very well with me.

## The New Hardware

Here's the hardware that I got:

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

## The Distro

I decided to play it safe and I went with Ubuntu 20.04, which was the latest release at the time.
Historically I preferred Arch Linux and Fedora, but I wanted to minimize the setup efforts.

## The Drivers

Everything worked out of the box, but unfortunately not everything worked
well. I was getting some intermittent issues with my wi-fi and X.org kept
crashing due to some issues with the GPU drivers. No matter what I tried (and I
tried a lot of things - updated kernels, MESA, random patches I found here and
there) I couldn't solve the GPU issues and my computer kept crashing 1-2 times
per day. As you can imagine this was quite frustrating.

Hardware support was the main reason I left Linux a decade ago and sadly the situation there
hasn't improved much. Perhaps the situation is better with Nvidia GPUs, but I was hoping
avoid dealing with a proprietary driver.[^1]

## The Apps

Little has changed in the past 10 years, not to mention I don't use that many apps - mostly a browser, Emacs and a terminal.
In short - everything was better than I remembered it, but nothing was fantastic. GNOME and its apps (e.g. Nautilus) still have plenty of rough edges, but they get the job done.

[Albert](https://github.com/albertlauncher/albert) was a great alternative to Alfred. That's probably the only new app that I used this time around.
Some proprietary apps that I need for work like Zoom, Slack and Dropbox didn't work/look great, but this is what I expected anyways. Still,
they were working better than I remember them to.

## The Development Experience

Absolutely divine. Proper package manager. Proper service management. Docker at its home. It doesn't get better than this.

## Epilogue

Due to the driver issues, in the end I've opted to switch to Windows 10 and WSL
(basically a Linux VM running in Windows). One year later I'm still a happy
camper.  With Windows 10 I got the best of both worlds:

- no driver issues
- proper Linux development experience

As a bonus - I also got the opportunity to play a lot more games.[^2]

Windows still has it's quirks, but the experience with it certainly beats the experience I had with Linux. All proprietary
apps work great, the drivers are solid and I'm essentially working in Linux all of the time. The only thing from macOS I'm really
missing is the nice collaboration/sync between all Apple devices - e.g. you take a picture on your phone and it immediately goes to your
computer. You get an SMS on your phone and it's also on your computer. Not a big deal, but this walled Apple garden kept me on macOS
even though I was getting more and more frustrated with it.

In short, hardware support remains the biggest weakness of Linux, just as always. It's super disappointing that even
after carefully choosing hardware for Linux compatibility I still ended up with a GPU that didn't work reliably.

On the bright side - it was a ton of fun to assemble a new PC, after all those years. You should have seen how happy I was when the thing booted
from the first try! And it was nice to check in on the progress made in the world of Linux and to find a reason to give Windows another chance.

That's all I have for you today. Keep hacking!

[^1]: And I happen to be a huge AMD fan.
[^2]: Not sure if that's really a bonus though.
