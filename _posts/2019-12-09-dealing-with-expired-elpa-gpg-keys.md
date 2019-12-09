---
layout: post
title: Dealing with Expired ELPA GPG Keys
date: 2019-12-09 11:53 +0200
categories: posts
tags:
- Emacs
- Meta Advent 2019
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

Another day of the Meta Advent, another article about Emacs.  Today we're going
to tackle one of the most annoying problems for Emacs users this year - the GPG
keys for GNU ELPA (Emacs's official package repository) bundled with all Emacsen
older than 26.3 recently expired, which means you can't access ELPA. Let's see if we can fix this.

In case you're wondering what are those GPG keys for - they are used by the ELPA package manager
(a.k.a `package.el`) to verify the authenticity of packages downloaded
from the GNU ELPA package archive. Basically, they ensure that no one has tampered with the package
that you're about to install. Those keys have a limited validity in time (for example, the first key was
valid until September 2019 only), so you need to update them from time to time.

Recently a ton of people were affected by the expiration of the original key in September, as
evidenced by tickets like [this one](https://github.com/bbatsov/prelude/issues/1225).
There are many ways to address the problem, with the simplest one being upgrading to Emacs 26.3.
Still, I think that even people on Emacs 26.3 would benefit from reading this article, as the keys
are going to expire in the future again.

Generally the best way to solve the problem is by installing the package `gnu-elpa-keyring-update`.
The package is available via ELPA and all you need to do is `M-x package-install gnu-elpa-keyring-update`.
Now you have to run `M-x gnu-elpa-keyring-update` and you're done. Easy, right?

Well, not so quickly. The problem is that if you realized you need this package,
chances are good that your GPG keys have expired already and you can't install
anything from ELPA. No worries, there are a couple of workaround for this.

## Updating the GPG keys manually

If you're not afraid of the command-line you can fetch the new key manually with a command like this:

``` shellsession
$ gpg --homedir ~/.emacs.d/elpa/gnupg --receive-keys 066DAFCB81E42C40
```

Alternatively you can modify the expiration date of the old key with something like:

``` shellsession
gpg --homedir ~/.emacs.d/elpa/gnupg --quick-set-expire 474F05837FBDEF9B 1y
```

That's one quick and simple approach. Let's now examine the alternative.

## Disable package signature verification

You can also instruct Emacs to stop verifying package signatures until you update the GPG keys.
That's done like this:

``` emacs-lisp
(setq package-check-signature nil)
```

After applying this setting, install the `gnu-elpa-keyring-update` package and update your keys. Don't forget
to re-enable signature verification afterwards:

``` emacs-lisp
(setq package-check-signature 'allow-unsigned)
```

That's the default of that particular setting - it verifies the signatures of signed packages, but also
allows you to install unsigned packages.

## Closing Words

I hope you'll find this article useful. I know I will - I've explained the problem to so many people so far
that I thought it'd be nice to be able to just point them to an article explaining it.

It'd be great if users were presented with easier to comprehend errors when they run into this problem, though.
I think that a simple issue was exacerbated a lot by the fact you just get a cryptic message saying some keys are
invalid and no pointers on how to address the problem. On the bright side - that's a great opportunity to
level up your googling skills.

That's all I had for you today! Until tomorrow!
