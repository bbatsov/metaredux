---
layout: post
title: Recording Screencasts with Emacs
date: 2019-12-08 13:30 +0200
categories: posts
tags:
- Emacs
- Meta Advent 2019
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

Yesterday I was reminded of one of my favourite aspects of blogging - you share
some knowledge, but you also gain a lot of knowledge in return. To be more
specific - several readers commented on my article [Dead Simple Emacs
Screencasts]({% post_url 2019-12-07-dead-simple-emacs-screencasts %}), that they
actually thought the article would cover how to record screencasts with Emacs
itself.  I have to admit this caught me by surprise, as even though I know well
enough that Emacs can do anything, I still never thought of trying to record
screencasts with it. I've been using tools like Apple Quicktime for conventional
screencasts and [LICEcap](https://www.cockos.com/licecap/) or
[Gifox](https://gifox.io/) for simple animated gifs. Let's see if we can do
better and record some awesome screencasts straight from Emacs!

<!--more-->

Well, it turns out that there's a mode for that![^1] It's named
[gif-screencast](https://gitlab.com/ambrevar/emacs-gif-screencast) and seems to
be a fairly new addition to the Emacs ecosystem, as its first commit is from
February 2018.  The package basically creates a single frame for each action you
take by making a screenshot. This simple approach has the nice benefit of
optimizing the size of the resulting gif, as it elimates awkward pauses when
you're not doing anything. As a bonus, it's possible to easily edit the end
result!  Here's a bit of the project's rationale straight from its README:

> Most of the time, one user action equals one visual change.  By exploiting this
> fact, this package tailors GIF-screencasting for Emacs and captures one frame
> per user action.  It’s much more efficient than capturing frames even at a
> frequency as low as 10 per second.  You could roughly expect 3 to 10 times
> smaller files compared to videos of the same quality.

Internally the mode shells out to several command-line tools that do the heavy lifting:

* a screenshot taking tool (by default that's `scrot` on Linux and `screencapture` on macOS)
* an image cropping tool (ImageMagick's `mogrify` by default)
* a tool to convert those screenshots into a gif (ImageMagick's `convert` by default).
* a tool to optimize the final gif (`gifsicle` by default). Alternative you can
just set `gif-screencast-want-optimized` to `nil`.

I guess it's clear at this point it's a good idea to install ImageMagick to have `gif-screencast` work well.
If you're using Linux you don't do much besides getting the external tools installed, but macOS users will need a bit of extra
setup. Put something like this in your Emacs config:

``` emacs-lisp
(with-eval-after-load 'gif-screencast
  (setq gif-screencast-args '("-x")) ;; To shut up the shutter sound of `screencapture' (see `gif-screencast-command').
  (setq gif-screencast-cropping-program "mogrify") ;; Optional: Used to crop the capture to the Emacs frame.
  (setq gif-screencast-capture-format "ppm") ;; Optional: Required to crop captured images.
```

If you're on a HiDPI display you'll also need to patch your `gif-screencast` installation a bit:

``` emacs-lisp
(advice-add
 #'gif-screencast--cropping-region
 :around
 (lambda (oldfun &rest r)
   (apply #'format "%dx%d+%d+%d"
          (mapcar
           (lambda (x) (* 2 (string-to-number x)))
           (split-string (apply oldfun r) "[+x]")))))
```

That's quite the setup, right? Don't worry, we're done with it!
Now you can finally do `M-x gif-screencast` and create your first screencast! Let's play a bit with CIDER:

![gif_screencast_demo.gif](/assets/images/gif_screencast_demo.gif)

When you're done with your screencast you have to do `M-x
gif-screencast-stop`. You can also pause a recording with `M-x
gif-screencast-pause`.  On macOS by default the screencasts get saved to
`~/Videos/emacs`. On Linux `XDG_VIDEO_DIR` would be used (typically it's
`~/Video`).

A couple of notes:

* For me at least Emacs became a bit slow during the screencast recording, but this wasn't a major issue.
* I've noticed that `gif-screencast` doesn't handle properly scrolling (e.g. in a REPL buffer).
* Cropping is broken on HiDPI displays. See [this issue](https://gitlab.com/ambrevar/emacs-gif-screencast/issues/14) for details.
* It's hard to show overlays that are immediately cleared on the next command. This is clearly visible in the example I showed earlier.
* Even with optimization enabled the resulting gif for me was much bigger than what I'd get from Gifox. Perhaps I did something
wrong.
* It's a good idea to bind the commands for starting, pausing and stopping screencasts to some keybindings:

``` emacs-lisp
(with-eval-after-load 'gif-screencast
  (global-set-key (kbd "<f8>") #'gif-screencast-toggle-pause)
  (global-set-key (kbd "<f9>") #'gif-screencast-stop))
```

Interestingly `emacs-gif-screencast` defines `gif-screencast-mode`, but doesn't bind any keybindings there
by default. That certainly seems like an opportunity for improvement to me.

So, all in all `gif-screencast` is a pretty cool tool that proves once again how versatile Emacs is.
Unfortunately, it's also a bit rough around the edges, so I'll probably stick with Gifox for the time being.
Still, I'm pretty excited about the possibilities created by `gif-screencast` and I'll certainly keep an eye on it.
That's all I have for you today! Emacs forever!

[^1]: What a shocker, right?
