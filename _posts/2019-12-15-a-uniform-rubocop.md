---
layout: post
title: A Uniform RuboCop
date: 2019-12-15 11:57 +0100
categories: posts
tags:
- Ruby
- RuboCop
- Meta Advent 2019
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

**Note:** I'll assume that if you're reading this post, then you probably already know what [RuboCop][] is, so I won't go into
any details on that subject.[^1]

As part of our ongoing efforts to get to a RuboCop 1.0 release,
recently we've been trying to address some internal inconsistencies -
most notably inconsistencies in cop names and cop configuration option names.
Ironically RuboCop is all about ensuring consistency, but it had
accumulated quite a few inconsistencies over the course of the years.
Here's a list of some common problems with cop names:

* We used "Unneeded" or "Redundant" interchangeably (now we always use "Redundant")
* We used "Blank" or "Empty" (now it's always "Empty")
* We used "IndentSomething" or "SomethingIndentation" (now it's always "SomethingIndentation")
* We weren't consistent in our usage of "Parameter" and "Argument". Those words are often considered to be synonyms, but actually
have different meanings and we wanted this reflected in RuboCop.

We also had a bunch of cops with really bad name that were hard to understand, so
we opted to rename those as well (e.g. `Naming/VariableNumber` became `Naming/VariableNumericSuffix`).
Here's a summary of all the cop renamings so far:

| Old Name                               | New Name                            |
|----------------------------------------|-------------------------------------|
| `Lint/UnneededCopDisableDirective`     | `Lint/RedundantCopDisableDirective` |
| `Lint/UnneededCopEnableDirective`      | `Lint/RedundantCopEnableDirective` |
| `Lint/UnneededRequireStatement`        | `Lint/RedundantRequireStatement` |
| `Lint/UnneededSplatExpansion`          | `Lint/RedundantSplatExpansion` |
| `Style/UnneededCapitalW`               | `Style/RedundantCapitalW` |
| `Style/UnneededCondition`              | `Style/RedundantCondition` |
| `Style/UnneededInterpolation`          | `Style/RedundantInterpolation` |
| `Style/UnneededPercentQ`               | `Style/RedundantPercentQ` |
| `Style/UnneededSort`                   | `Style/RedundantSort` |
| `Layout/AlignArguments`                | `Layout/ArgumentAlignment` |
| `Layout/AlignArray`                    | `Layout/ArrayAlignment` |
| `Layout/AlignHash`                     | `Layout/HashAlignment` |
| `Layout/AlignParameters`               | `Layout/ParameterAlignment` |
| `Layout/IndentAssignment`              | `Layout/AssingmentIndentation` |
| `Layout/IndentFirstArgument`           | `Layout/FirstArgumentIndentation` |
| `Layout/IndentFirstArrayElement`       | `Layout/FirstArrayElementIndentation` |
| `Layout/IndentFirstHashElement`        | `Layout/FirstHashElementIndentation` |
| `Layout/IndentFirstParameter`          | `Layout/FirstParameterIndentation` |
| `Layout/IndentHeredoc`                 | `Layout/HeredocIndentation` |
| `Layout/LeadingBlankLines`             | `Layout/LeadingEmptyLines` |
| `Layout/TrailingBlankLines`            | `Layout/TrailingEmptyLines` |
| `Lint/DuplicatedKey`                   | `Lint/DuplicateHashKey` |
| `Lint/HandleExceptions`                | `Lint/SuppressedException` |
| `Lint/MultipleCompare`                 | `Lint/MultipleComparison` |
| `Lint/StringConversionInInterpolation` | `RedundantStringCoercion` |
| `Naming/UncommunicativeBlockParamName` | `Naming/BlockParameterName` |
| `Naming/UncommunicativeMethodParamName`| `Naming/MethodParameterName` |

**Note:** Those changes were carried out in RuboCop 0.76 and RuboCop 0.77.

We've also spent some efforts on removing all remaining usages of `Blacklist` and `Whitelist` in the cop
parameters. Originally it was suggested to do a blanket rename of those to `Denylist` and `Allowlist`, but
we figured out we could do better. In the end all generic names became something much easier to
understand - e.g. `AllowedNames`. You can see everything impacted by this change [here](https://github.com/rubocop-hq/rubocop/pull/7469).

If you'd like to learn more about the standardization efforts, here's the [ticket](https://github.com/rubocop-hq/rubocop/issues/7077)
that tracks those. At some point we'll need to mention the naming guidelines somewhere in RuboCop's
documentation. We'll also have to create a few "Internal Investigation" cops to enforce those
within the project's codebase.

I know that no one likes breaking changes, but this needed to be done.[^2]
I guess if you didn't have some custom configuration for the cops
that were impacted you didn't even notice the changes. On the bright side -
RuboCop 1.0 is closer than ever and so is the stability that you've been
asking for a while now.

We've got other unification efforts going on as well - e.g. cop documentation,
cop messages, adopting the AST node pattern matching approach everywhere it
makes sense to do so and the specs. It's not the most fun and exciting work, but
it's quite important to the long-term well-being of the project. If you notice
any inconsistencies that we need to address, please let us know.

I plan to write a couple more articles about the final preparations for
RuboCop 1.0. Stay tuned for more updates!

[RuboCop]: http://rubocop.org
[^1]: For those of you who don't know - it's a popular Ruby static code analyzer and formatter.
[^2]: All breaking changes are clearly marked with (**Breaking**) in the Changelog.
