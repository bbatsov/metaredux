---
layout: post
title: 'Hard CIDER: Keeping your REPL Tidy'
date: 2019-05-11 18:13 +0300
categories: posts
tags:
- Clojure
- Emacs
- CIDER
- Series
- Hard CIDER
---

Only one of the following two statements is correct:

* Working with [CIDER](https://docs.cider.mx) is hard!
* Hard cider is the best cider!

I'll let you decide which one yourselves.

"Hard CIDER" is a new blog series dedicated to demystifying the dark
art of using CIDER effectively and convincing you that the second
statement is the correct one. I was thinking that a more appropriate
name for the series might have been "Effective CIDER" or "A Pint of
CIDER", but I'm not a particularly appropriate person. And naming is
hard![^1]

Here we go...

## The Problem

CIDER's REPL buffer is known to experience slowdowns when a lot of
output accumulates in it.  That's not a CIDER-specific issue, but more
like a general Emacs issue. We've tried hard to tune the REPL's
performance in [CIDER 0.21 (New York)]({% post_url
2019-04-01-new-york-new-your %}), but it would still grind to a halt if
you dump a few megabytes of output in it.

I've noticed that many CIDER users would simply restart either CIDER or Emacs when faced with this issue, but there
are a few small simple steps you can take to mitigate the problem.

## The Solution

### Clear the Most Recent Output

If you happen to evaluate something that produced a huge result or a lot of console output there's a very simple solution to that.
Just press `C-c C-o` (or `M-x cider-repl-clear-output`). Running the command in this REPL:

``` clojure
;; That's not going to end well...
user> (take 5000000 (repeatedly (fn [] "Emacs Forever!")))
("Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 "Emacs Forever!"
 ;; Five more million lines of output
user>
```

Will result in:

``` clojure
user> (take 5000000 (repeatedly (fn [] "Emacs Forever!")))
;; output cleared

user>
```

An epic mistake has been undone and your REPL is back to normal!

**Note:** This command will remove both the value produced by the last evaluation plus any console output that originated from it.

## Clearing the REPL Completely

From time to time it's a good idea to just clear your REPL buffer completely. CIDER naturally has a command for that - `C-u C-c C-o` (`M-x cider-repl-clear-buffer`).
I usually run it every few hours, as I rarely care much about things I've evaluated a while ago (plus I can easily retrieve them from the REPL history).

This command nukes everything in the REPL buffer and resets it to pristine brand-new state. It's the ultimate recipe to combat REPL sluggishness.

## Interrupting Evaluations

If you notice that some evaluation is producing a lot of output it's usually best to kill it right away, instead of waiting for the output to
cripple your REPL. Let's revisit this example:

``` clojure
;; That's not going to end well...
user> (take 5000000 (repeatedly (fn [] "Emacs Forever!")))
```

Your best course of action after seeing that the result is huge is to press `C-c C-c` (`M-x cider-interrupt`).
That way you'd get only a few hundred (thousand) lines of output instead of 5 million.[^2]
Typically I'll follow such an interrupt with `C-c C-o` to clear the partial output, which may still be fairly substantial.

## Epilogue

That's all I have for you today. Hard CIDER is off to a simple start!
I might add some short screencasts and animated gifs to future episodes, but I won't be making
any promises. The format is brand new and it will take me a while to find the optimal recipe for it.
Your feedback will be instrumental in that process!

Until next episode! In the mean time - keep hacking!

[^1]: Just like good cider.
[^2]: It all depends on your reflexes and your hardware.
