---
layout: post
title: Smarter Form Targeting Is Not Coming to CIDER
date: 2026-08-29 09:37 +0300
tags:
- Emacs
- Clojure
- CIDER
---

A couple of days ago I wrote that [smarter form targeting was coming to CIDER]({% post_url 2026-08-26-smarter-form-targeting-is-coming-to-cider %}),
and I ended that post by asking whether I'd got the resolution rules right and
whether anything still surprised people. I got an answer. CIDER 2.1 will ship
with the classic behaviour intact.

<!--more-->

This is not a sad story, though. The detour turned up a bug that was quietly
mangling people's comments, and CIDER came out of it better than it went in.

## What I was actually after

The targeting change wasn't really about cursor positions. What I wanted was
for *every* CIDER command that operates on a form to behave the same way,
without adding yet another command to get there. CIDER has an enormous surface
of evaluation commands, and every "at point" variant I could have added would
have made that worse. If the existing commands simply resolved the form you
meant, newcomers would have had fewer commands to learn, not more.

That was the bet: consistency by redefinition rather than by addition. In
hindsight it was the wrong bet for a project this old. Fifteen years in, the
existing behaviour isn't an implementation detail I get to tidy up - it's
the contract. And as it turned out, the redefinition wasn't nearly as
transparent as I'd convinced myself it was.

## The feedback

Several users, including CIDER's co-maintainer Sashko Yakushev, voiced
concerns and flagged issues I'd overlooked while playing with this initially.
The most important one: inspection is just another flavour of evaluation, and
people inspect bare symbols constantly. If it got the same treatment, the
disruption would be real enough to run a fork over.

My instinct was that this was an edge case, so I measured it instead of
arguing. Across every cursor position in a buffer, only three rules actually
differ, and the flagship flow - type a form, hit `C-x C-e` - is identical
under both. Two of those three were fine. The third was this one:

![Cursor on a closing paren: the classic rules evaluate the last form inside, smart targeting evaluates the whole enclosing call](/assets/images/cider-closing-paren.gif)

The cursor doesn't move between those two evaluations. That's the same
position, twice, and the answers differ - `"b"` under the classic rules,
`"ab"` under the new ones.

I'd filed "cursor on a closing paren" as an oddity nobody hits deliberately.
But think about when you land there: you finish typing the last thing inside
a form, and the cursor is now sitting on the `)`. For someone inspecting
symbols all day, that fires constantly. And the classic answer isn't
arbitrary either - as Sashko put it, the rule of thumb is "whatever
`paredit-backward` jumps back to", which is a better description of the
tradition than anything I'd written down.

## Why the tradition exists in the first place

Here's the part I under-weighted, and it's worth spelling out for anyone who
finds "the form *before* the cursor" arbitrary.

Emacs form navigation overwhelmingly leaves the cursor *after* a form.
`C-M-f` (`forward-sexp`) moves over the next form and stops just past its
closing delimiter. `C-M-e` (`end-of-defun`) leaves you after the whole
top-level form. `C-M-n` (`forward-list`) does the same for the enclosing
list. Paredit's `paredit-forward` behaves the same way, and so does typing:
finish a form and the cursor is, by definition, right after it.

So "evaluate the preceding form" isn't a quirk - it *composes* with how you
already move around. Navigate forward over a form, evaluate it. Type a form,
evaluate it. The cursor is already in the right place, every time.

Getting *onto* a form instead takes deliberate effort: `C-M-b`
(`backward-sexp`), `C-M-a` (`beginning-of-defun`), `paredit-backward`, or a
jump package like `avy`. All perfectly good tools, but you have to reach for
them - unless you're clicking around with a mouse, in which case the cursor
lands wherever you pointed and "the form before the cursor" genuinely is
useless. Which, I suspect, is exactly the workflow difference behind this
whole argument.

## What CIDER got instead

The half of the idea that was never controversial is still there, as commands
you opt into rather than a new meaning for keys you already use. Every
operation now has an "at point" variant, not just eval and tap:
`cider-inspect-sexp-at-point`, `cider-pprint-eval-sexp-at-point`,
`cider-macroexpand-1-at-point`, `cider-macroexpand-all-at-point`,
`cider-format-edn-sexp-at-point`, `cider-insert-sexp-at-point-in-repl`.

Three ways to say which form you mean, and now every command supports all of
them:

![The same expression evaluated three ways: the preceding form, the form at the cursor, and the enclosing top-level form](/assets/images/cider-form-selection.gif)

The at-point commands fall back to the preceding form when there's nothing to
point at, so they're drop-in replacements rather than a separate mode of
working. Which means anyone who wanted smart targeting can simply have it:

```emacs-lisp
(with-eval-after-load 'cider-mode
  (define-key cider-mode-map (kbd "C-x C-e") #'cider-eval-sexp-at-point)
  (define-key cider-mode-map (kbd "C-c C-e") #'cider-eval-sexp-at-point))
```

Two lines, no hidden mode, and everyone else's fingers keep working. This is
what I should have shipped in the first place, and it's what the
[manual](https://docs.cider.mx/cider/usage/code_evaluation.html) now
recommends. Yes, it's more commands than I wanted. It's also the version that
doesn't break anyone.

Macroexpansion is the one place a bit of cleverness survived on its own
merits, because an expansion needs a call form. Stand on a bare symbol and
`cider-macroexpand-1-at-point` widens to the call around it, since expanding a
lone symbol is never what anyone meant.

## The bug at the bottom of the hole

While unifying the plumbing I found this, which is much worse than anything
form targeting was ever guilty of. Put the cursor at the end of a comment:

```clojure
(defn foo [])
;; a comment|
```

CIDER answered `comment`. Not the `(comment ...)` form - the *word*, lifted
out of your prose, because sexp motion has no notion of comments once the
cursor is inside one and happily reads the words as symbols.

For evaluation that produced a puzzling error. For the in-place macroexpansion
commands, which *replace* the region they resolved, it did this:

```clojure
;; a comment          ->   ;; a EXPANDED<comment>
(+ 1 2) ; hey         ->   (+ 1 2)          ; EXPANDED<hey>
```

It rewrote the comment. That bug has been in CIDER for years, and I only found
it because I went looking at the primitives while cleaning up after myself.

Then I checked the neighbours, and this is my favourite part of the whole
episode: **SLIME, SLY and Emacs Lisp itself all still do this.** Both Lisp
environments use a bare `backward-sexp`, and if you put the cursor after
`(+ 1 2) ; hey` in any Emacs Lisp buffer and ask for the preceding sexp, you
get `hey`. CIDER now steps out of the comment first, which as far as I can
tell makes it the only one of the family that gets this right.[^1]

## One idea worth stealing

The same survey turned up something CIDER was missing. SLY briefly flashes the
region it compiled, so you *see* what it acted on. That's a direct answer to
the confusion this whole saga was about - "which form did it just take?" -
and it changes nothing about what gets taken.

```emacs-lisp
(setq cider-flash-evaluated-region t)
```

Off by default, because I've learned my lesson about switching things on for
everyone. But if the targeting rules ever puzzle you, turn it on for a day.

## The moral

In the original post I described `cider-form-targeting`, the escape hatch back
to the classic rules, as "living on borrowed time" - clutter left over from a
plan I'd since reversed, which I was itching to delete.

That option is the only reason the conversation stayed a conversation. Its
existence made the objection "please don't remove the fallback" rather than
"I'm forking CIDER", and I very nearly removed it before anyone had tried the
change.

Every mistake is a learning experience for me, and this one was cheap: nothing
had shipped, so the whole thing cost a few days and some rewriting. The
testing and feedback cycle worked exactly as it should have - people tried
something on `master`, told me plainly what was wrong with it, and the result
is better than either what I proposed or what we had before. Everyone gets
the behaviour they want, and CIDER lost a text-eating bug on the way.

Thanks to everyone who took the time to tell me I was wrong.

That's all I have for you today. Keep hacking!

[^1]: If you're an Emacs maintainer reading this: `elisp--preceding-sexp` has the same behaviour, and I'd be happy to be told why it's intentional.
