---
layout: post
title: 'Hard CIDER: Easy Downgrading'
categories: posts
tags:
- Clojure
- Emacs
- CIDER
- Series
- Hard CIDER
---

Welcome to another installment of the "Hard CIDER" series! It's been way too long since the previous
episode and it's time to get back into action. Today we're going to discuss the grizzly subject
of downgrading CIDER.

## Downgrading from a MELPA (snapshot) release

Due to the massive popularity of MELPA[^1] a many CIDER users are installing it from there
and are effectively using a snapshot version. While most of the time the snapshot version works
fine[^2], from time to time you might run into some (small) issues (or epic mistakes) there.[^3] This section of the article
will teach you what to do if you ever end up in this nasty situation - namely downgrade to the latest stable
CIDER version.

The downgrade process is really simple. You just need to remove the `cider` package, pin `cider` to MELPA Stable
and reinstall it. Basically you need to do the following:

* `M-x package-remove cider`
* Add `(add-to-list 'package-pinned-packages '(cider . "melpa-stable") t)` to your Emacs config
* Restart Emacs (or reload your config)
* `M-x package-install cider`

Of course, you'll have to add the MELPA Stable package repo to your Emacs config if you haven't done so already. Here's
a complete `package.el` configuration that you can reuse if you want to:

``` emacs-lisp
(require 'package)

(add-to-list 'package-archives
             '("melpa" . "https://melpa.org/packages/") t)
(add-to-list 'package-archives
             '("melpa-stable" . "https://stable.melpa.org/packages/") t)
;; keep the installed packages in .emacs.d
(setq package-user-dir (expand-file-name "elpa" user-emacs-directory))
(package-initialize)
;; update the package metadata is the local cache is missing
(unless package-archive-contents
  (package-refresh-contents))
```

Depending on the type of person you are, you might also want to consider sticking permanently with CIDER pinned to
MELPA Stable. Probably the only real problem with this approach is that CIDER releases tend to be few and far in
between and you might end up waiting a very long time for some (important) bugfix or a cool new feature.[^4]

## Downgrading only cider-nrepl

In case you experience a problem with `cider-nrepl` you don't really need to downgrade the Emacs `cider` package - you can simply
instruct CIDER to use an older version of the middleware. Let's assume that `cider-nrepl-0.22-beta6` was faulty and we want to go back to `0.22-beta5`. Here's how to do this:

    (setq cider-required-middleware-version "0.22-beta5")

After you've updated your Emacs config you'll need to reload it and restart CIDER. Easy-peasy, right?

## Downgrading from a Stable Release

Imagine you're using CIDER 0.21 and for some reason you want to go back to CIDER 0.20. How can you do this?

That's going to be a pretty short section, as that's simply not possible.
Unfortunately `package.el` doesn't support the concept of historical versions of a package -
there's always only one version that's available - the latest one.

Fortunately, I don't think that this is something that many people needed to do, and if you really **have to** do it - it's doable, but painful.
You'll have to do the installation completely manually by cloning the relevant CIDER release tag locally and loading CIDER from there (and potentially some of its dependencies like `clojure-mode`).

## Closing Thoughts

I was inspired to write this article after breaking CIDER's snapshot for a couple of hours on Monday. Shortly afterwards I got asked
for the millionth time what to do in this situation and here we are. I also took a moment to add those instructions to the [FAQ](https://docs.cider.mx/cider/faq.html#_can_i_downgrade_cider_from_a_snapshot_build_to_the_latest_stable_version) for future reference.

That's all I had for you today! Keep hacking!

[^1]: The underlying cause of this is mostly the fact the historically Emacs package maintainers had a tendency to release extremely rarely (if ever).
[^2]: I really hope this is not just wishful thinking on my part.
[^3]: Typically serious problems are addressed within several hours of being reported.
[^4]: Lately I've been trying hard to improve the release cadence, and presently things are looking better on this front.
