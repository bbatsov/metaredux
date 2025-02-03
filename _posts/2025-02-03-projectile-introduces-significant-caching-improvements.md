---
layout: post
title: Projectile Introduces Significant Caching Improvements
date: 2025-02-03 11:23 +0200
tags:
- Emacs
- Projectile
---

One of Projectile's key features has always been the ability to cache the files in a project,
so next time need to do something with them you'd skip the potentially expensive project file
indexing step. The feature has always worked reasonably well, but it suffered from a couple of design
flaws that I've been meaning to address for a while:

- The cache for all projects is stored in the same file, which means that often you need to
load information that you don't really need. Also - storing all the cache data in the same file
means that there's a higher chance that the cache would get corrupted for one reason or another.
- When caching is enabled, Projectile loads the data from the cache file when `projectile-mode`
is enabled. That causes two problems:
  - The mode's init is slower than it could be
  - The cache is not loaded unless you enable `projectile-mode`

I'm happy to report that I finally got to doing something about the problems listed above![^1] In a nutshell:

- Now each project gets its own cache file in the root of the project's
  directory. (named `.projectile-cache.eld` by default)[^2] I've opted for this
  approach over something like `.emacs.d/cache`, as it avoids the need to hash
  the project paths. (so cache files could be mapped to project root paths) I
  might change this behavior in the future, depending on the user feedback I
  receive.
- The cache is read from disk only when you trigger `projectile-find-file` for the first time.
- Both reading and writing the cache file is much faster, as you're only
reading/writing the cache file for the currently visited project.

This means that Projectile's loading time is much snappier if you're a heavy cache user.
I plan more improvements in this area - e.g. adding an option to keep the cache only in memory (without writing it to disk),
which is probably fine for most people.

To make things better I've also reorganized how known projects are initialized, so now no IO operations
happen during Projectile's initialization and it's lightning fast! To illustrate this with a bit
of code:

```elisp
;; projectile-mode init before
;;
;; initialize the projects cache if needed
(unless projectile-projects-cache
  (setq projectile-projects-cache
        (or (projectile-unserialize projectile-cache-file)
            (make-hash-table :test 'equal))))
(unless projectile-projects-cache-time
  (setq projectile-projects-cache-time
        (make-hash-table :test 'equal)))
;; load the known projects
(projectile-load-known-projects)
;; update the list of known projects
(projectile--cleanup-known-projects)
(when projectile-auto-discover
  (projectile-discover-projects-in-search-path))

;; projectile-mode init now
;;
;; nothing IO-related to show here
```

Last week I've started to prepare for the release of Projectile 2.9, which will bring a lot of
bug fixes and improvements. I know that a lot of people have probably written off Projectile by
now, but I still believe that it has a place in the broader Emacs ecosystem (as a community-driven and more
feature-rich alternative of `project.el`) and the best is yet to come!

That's all I have for you today. Keep hacking!

**P.S.** I'd appreciate it if more Projectile users experimented with the current snapshot package, before
Projectile 2.9 gets officially released.

[^1]: See <https://github.com/bbatsov/projectile/commit/5061bd8dcd9f4d0e874884272f88b10892d15da3>.
[^2]: You'll probably want to add this file to your `.gitignore`. Or not, as the paths stored in it are relative to the project's root, so they can actually be reused across different machines.
