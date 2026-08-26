---
layout: post
title: Smarter Form Targeting Is Coming to CIDER
date: 2026-08-26 12:18 +0300
tags:
- Emacs
- Clojure
- CIDER
---

If I had a dollar for every time someone asked on the Clojurians Slack in
`#cider` why `C-x C-e` evaluated "the wrong thing", I'd probably be writing this
post from a yacht.  The answer was always the same: the cursor wasn't where
CIDER expected it to be. The *upcoming* CIDER 2.1 release changes that - the
evaluation commands now figure out which form you mean from where your cursor
actually is.

<!--more-->

## A bit of history

Emacs has a very particular tradition when it comes to evaluating code:
`eval-last-sexp` (the venerable `C-x C-e`) acts on the expression *before* the
cursor. Not the one you're looking at, not the one you're inside of - the one
that ends exactly where your cursor stands. SLIME follows this tradition,
Emacs Lisp itself follows it, and for the past 15+ years CIDER has followed it
too.[^1] If you grew up in Emacs, this rule is in your fingers and you've
never once thought about it.

Here's the thing, though - most people using CIDER didn't grow up in Emacs.
And many of them never programmed in Emacs Lisp and Common Lisp with SLIME.
They came to Emacs *because of* CIDER (or Clojure in general), and for them
the rule is invisible, arbitrary and mildly hostile. You put your cursor on a
form, you press the eval key, and CIDER cheerfully evaluates... something
else. Meanwhile every other modern Clojure environment - Calva, Conjure, the
various vim plugins - resolves the form from the cursor position and just does
what you meant.[^2]

For a long time I resisted changing this, mostly out of respect for the Emacs
tradition (and my own muscle memory). But at some point I had to admit that I
was optimizing for the wrong audience. CIDER's users are mostly casual
Clojure hackers who happen to use Emacs, not Emacs experts who happen to
write Clojure. The tradition was serving me, and confusing them.

## What's actually changing

The evaluation commands (and their macroexpansion, inspection and tapping
siblings) now resolve "the form the cursor indicates". Concretely, with `|`
marking the cursor:

```clojure
(map inc |(range 10))
```

Pressing `C-c C-e` here used to evaluate `inc` - the form *before* the cursor,
which is almost never what you wanted. Now it evaluates `(range 10)` - the
form your cursor is pointing at.

```clojure
(str "hello" " " "world"|)
```

This one used to evaluate `"world"` (really!), because the last complete
expression before a cursor sitting on the closing paren is the final string.
Now it evaluates the whole `(str ...)` call.

Macroexpansion benefits too:

```clojure
(when tru|e (launch-missiles))
```

`C-c C-m` here used to complain that `true` is not a macro. Now it expands the
enclosing `(when ...)` call, because expanding a bare symbol is never what
anyone means.

And my favorite one - the rich comment workflow is now consistent everywhere:

```clojure
(comment
  (calculate-all-the-things|))
```

Every defun-level command - eval, pretty-print, inspect, debug - now treats
the form inside the `(comment ...)` as the top-level one. Evaluating a whole
`comment` form returns `nil` by definition, which has exactly zero uses, so
CIDER no longer does that no matter which command you reach for.

## Why you probably won't notice

Here's the part I'm most pleased with: the new behavior agrees with the old
one at every position where "the form before the cursor" made sense. Cursor
right after a form? Same result as always. Cursor in the whitespace after a
form? Same. Cursor in the middle of a symbol? Same. The two behaviors only
diverge where the classic answer was something nobody ever wanted - a
previous sibling, a lone trailing atom.

So if your muscle memory follows the Emacs tradition, nothing changes for
you. If it doesn't - CIDER stops punishing you for it. That's the whole
change.

## Reverting to the classic behavior (for now)

If you *do* want the traditional rules - maybe you genuinely use
"evaluate the previous sibling while standing on an opening paren" - one
setting restores them exactly:

```emacs-lisp
(setq cider-form-targeting 'preceding)
```

There's also a per-session toggle in the eval menu (`C-c C-v T`) that shows
the active mode in the mode line while you experiment.

This option is probably living on borrowed
time, though. I added it back when I planned to keep the classic behavior as the
default and offer smart targeting as an opt-in. Now that the roles are
reversed, an option whose only job is restoring rules almost nobody
deliberately relied on doesn't really make much sense, and lately I've been
trying to trim that kind of clutter from CIDER, not add to it. Don't be
surprised if the option quietly disappears - possibly even before the release
ships. Which is one more reason to speak up now if the classic behavior
genuinely matters to you.

## Farewell, "last sexp"

This change forced my hand on something I'd been putting off for years - the
command names. `cider-eval-last-sexp` is a fine name for a command that
evaluates the last sexp. It's a lie for a command that evaluates the form
your cursor indicates. So the commands got honest names:

- `cider-eval-last-sexp` is now `cider-eval-form`
- `cider-eval-defun-at-point` is now `cider-eval-defun` (the `-at-point`
  never carried information)
- likewise for the pprint/tap/inspect/insert variants

Every old name keeps working as an alias, so your config and your `M-x`
habits are safe. But why "form" and not "sexp"? Beyond the targeting change,
there's a Clojure-specific reason: in Clojure a *form* isn't always a single
sexp. `^:private x` is two sexps but one form; so is `#inst "2024-01-01"`.
The commands operate on forms - the reader's unit of evaluation - and now
they say so.[^3] The manual's evaluation docs got a proper glossary
explaining all of this.

## Closing thoughts

All of this is on `master` and in the MELPA snapshots today, ahead of the
next stable release. I'd really love for people to play with it *before* the
release ships - especially if you're an Emacs veteran whose fingers disagree
with my reasoning, or a newcomer for whom this was supposed to just work. Did
we get the resolution rules right? Does anything still surprise you?

Share your feedback on the [CIDER discussions][discussions] board, in
`#cider` on the Clojurians Slack, or just file an issue. This is exactly the
kind of change that's easy to adjust before a release and painful after -
and the fate of the compatibility option depends on what I hear.

That's all I have for you today. Keep hacking!

[discussions]: https://github.com/clojure-emacs/cider/discussions

[^1]: CIDER started its life as a SLIME "clone" for Clojure, after all - the tradition runs deep.
[^2]: Interestingly, Cursive is the only major non-Emacs Clojure environment that kept the classic "form before the caret" model.
[^3]: This also explains a subtlety Emacs veterans might appreciate: plain `forward-sexp` movement doesn't know that Clojure metadata belongs to the form it annotates, which is why clojure-mode has always needed its own "logical sexp" movement functions. The new targeting is built on those, so metadata is never silently dropped from what you evaluate.
