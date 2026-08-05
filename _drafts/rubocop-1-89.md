---
layout: post
title: RuboCop 1.89: Project-Wide Analysis with Rubydex
date: 2026-08-05 10:00 +0300
tags:
- Ruby
- RuboCop
---

[RuboCop 1.89][release] is out, and it comes with the usual assortment of new cops
and bug fixes. But there's one theme running through this release that I'm
particularly excited about, and that's what I want to talk about today: RuboCop
is finally learning to look beyond a single file.

<!--more-->

## The single-file curse (limitation)

For its entire existence RuboCop has analyzed your code one file at a time. That's
not an accident - it's a deliberate design choice that has served the project
extremely well. A file-at-a-time analyzer is simple, it's fast, and it
parallelizes trivially across all your cores. It's a big part of why RuboCop can
lint a huge codebase in a few seconds.

The catch is that a cop looking at a single file is essentially working with
blinders on. It has no idea what's defined in the rest of your project. And that
turns out to be a real limitation for a whole class of checks:

- Is this method a duplicate of one defined in another file? No way to tell.
- Does this constant actually refer to what I think it does, given the surrounding
  namespaces? Can't resolve it without the whole picture.
- Does this class really need a `super` call in its constructor, or does none of
  its ancestors define `initialize`? RuboCop can't see the ancestry, so it has to
  play it safe.

Faced with questions like these, cops have only ever had two options: bail out and
say nothing (a false negative), or guess and risk yelling at perfectly good code
(a false positive). Neither is great. Over the years we've papered over this with
configuration knobs - things like `AllowedParentClasses` for `Lint/MissingSuper`,
where you manually list the base classes RuboCop can't see for itself. It works,
but it's the kind of busywork a tool should be doing for you.

## Enter rubydex

[rubydex][rubydex] is a library from Shopify that does exactly what RuboCop
historically couldn't: it builds a project-wide index of your code - a graph of
every declaration and reference, with the ancestry and resolution worked out. Give
a cop access to that index and suddenly it can answer all those cross-file
questions with confidence.

We introduced optional support for rubydex back in [1.87][project-index], and 1.89
is where it really starts to pay off.

I want to stress the word *optional* here. rubydex is a separate gem with native
(Rust) extensions, and using it means paying for an indexing pass and some extra
memory. That's a perfectly reasonable trade for the extra smarts, but it's not a
trade everyone wants to make, and it shouldn't be forced on anyone. RuboCop's
default experience should stay lean and dependency-light - no native code, no
index, no surprises. So rubydex is opt-in, and I fully expect it'll stay that way
for the foreseeable future. If you want the cross-file superpowers, you flip a
switch; if you don't, nothing changes for you.

Turning it on is two steps. Add the gem to your `Gemfile`:

```ruby
gem 'rubydex', require: false
```

And enable the flag in your `.rubocop.yml`:

```yaml
AllCops:
  UseProjectIndex: true
```

Everything below happens automatically once the index is available - there's no
per-cop opt-in. And if the gem isn't installed, RuboCop just prints a friendly
warning and falls back to its usual file-local behavior.

## What the index unlocks in 1.89

This release wires the index into a good chunk of the cop library. Here's a tour
of what you get.

There are a few brand-new cops that simply couldn't exist without a project-wide
view:

- `Lint/NameTypo` is my personal favorite - it catches typos in constant and
  method names by checking them against everything defined in your project and
  suggesting the closest match. So `Services::UserCraetor.new` gets flagged with a
  helpful "did you mean `UserCreator`?", and the same goes for a fat-fingered
  method call like `Report.generate_sumary`. It's careful to only speak up when
  it's confident (the name has to be unresolvable *and* have a close sibling), so
  it stays quiet on genuinely external names.
- `Lint/DeprecatedReference` flags calls and constant references to any API you've
  marked with a YARD `@deprecated` tag, anywhere in the project. Tag a method as
  deprecated in one file, and every stray use of it elsewhere lights up. It's a
  lovely way to drive a gradual migration off some old code.
- `Lint/UnusedPrivateMethod` does project-wide dead-code detection - it reports
  private methods that are never referenced anywhere in the indexed project. It's
  disabled by default and best used as an occasional sweep, since it can't see
  reflective calls, but it's great for a spring cleaning.

A whole bunch of existing cops got sharper too, either catching things they used
to miss or backing off where they used to nag:

- `Lint/DuplicateMethods` now catches duplicates that live in *different* files -
  the same method defined twice across two files was previously invisible to it.
  If you're deliberately redefining a method (a monkey patch, say), the self-alias
  trick (`alias foo foo` right before the redefinition) signals your intent and
  keeps things quiet.
- `Lint/ConstantReassignment` likewise catches a constant that's reassigned in
  another file, not just within the current one.
- `Lint/MissingSuper` no longer nags you about a missing `super` when the index can
  prove that none of the class's ancestors actually defines `initialize` - in that
  case `super` would only reach the no-op `Object#initialize`. This is the one that
  makes those `AllowedParentClasses` lists mostly unnecessary; RuboCop figures out
  your abstract base classes on its own now.
- `Lint/ConstantResolution` used to be so noisy it was practically unusable without
  a pile of `Only`/`Ignore` configuration. With the index it reports only genuinely
  ambiguous constants - ones that resolve differently through the surrounding
  nesting than they would fully qualified - which finally makes it practical to
  turn on.
- `Lint/InheritException` now spots classes that inherit from `Exception`
  indirectly, through a parent class defined elsewhere in your project.
- `Style/Documentation` accepts a reopened class or module as documented as long as
  *any* of its definition sites carries a doc comment, so you're not forced to
  repeat yourself at every reopening.
- A cluster of cops learned to stop producing false positives once they can see the
  whole picture: `Style/MissingRespondToMissing` accepts a `respond_to_missing?`
  defined in another reopening of the class; `Style/StaticClass` leaves alone
  classes that are subclassed elsewhere; `Style/RedundantConstantBase` can now flag
  a redundant leading `::` inside a namespace when it proves the constant resolves
  identically without it; and `Naming/PredicatePrefix` and
  `Naming/AccessorMethodName` stop suggesting renames for methods that override an
  ancestor defined elsewhere in the project.
- `Style/ClassAndModuleChildren` uses the index to make its (unsafe) autocorrection
  a lot more reliable - it resolves the real namespace kind (`class` vs `module`)
  instead of guessing, and skips compacting a definition when doing so would blow
  up with a `NameError` at load time.

And a couple of things happening under the hood are worth calling out:

- The index now always covers your whole project, regardless of which files a
  particular run happens to inspect. That means linting a single file reports the
  same cross-file offenses as a full run - no more results that depend on how you
  invoked RuboCop.
- If you want ancestry chains that reach into your gems to resolve too (think a
  model inheriting from a framework base class), the new
  `AllCops/ProjectIndexIncludesGems` option indexes your bundle's sources as well.
  On RuboCop's own repo that took the share of classes with a fully resolvable
  ancestry from about 20% to about 97%, at the cost of some extra memory.
- The language server got smarter about the index - it now builds it once per
  session and reuses it across requests, rebuilding only when you save a file, so
  editing a buffer doesn't pay the indexing cost on every keystroke.

## Still early days

I don't want to oversell any of this. We're at the very beginning of RuboCop's
cross-file journey, and there's a lot we haven't tackled yet. The list of checks
that would benefit from a project-wide view is long, and I suspect the most
interesting applications are still ahead of us. rubydex itself is young and
evolving, and so is our integration with it.

But I think it's a genuinely promising start. A year ago the idea of RuboCop
catching a constant typo across files, or reasoning about a class's real ancestry,
would have been a non-starter. Now it's a config flag away.

If any of this sounds useful, I'd love for you to give it a try - add `rubydex` to
your `Gemfile`, flip `UseProjectIndex` on, and see what it turns up in your
codebase. And please [tell us how it goes][issues]. The feature is still marked
experimental, and real-world feedback is exactly what we need to figure out where
to take it next. Bug reports, false positives, ideas for new cross-file cops - all
of it is welcome.

Big thanks to everyone who has been kicking the tires on the project index and
reporting their findings. Here's to seeing beyond a single file.

Keep hacking!

[release]: https://github.com/rubocop/rubocop/releases/tag/v1.89.0
[rubydex]: https://github.com/Shopify/rubydex
[project-index]: https://docs.rubocop.org/rubocop/usage/project_index.html
[issues]: https://github.com/rubocop/rubocop/issues
