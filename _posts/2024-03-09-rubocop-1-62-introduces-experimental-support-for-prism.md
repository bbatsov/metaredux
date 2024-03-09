---
layout: post
title: RuboCop 1.62 Introduces (Experimental) Support for Prism
date: 2024-03-09 12:12 +0200
tags:
- Ruby
- RuboCop
- Prism
---

Recently I wrote that it was already possible to run RuboCop with Ruby's new
Prism parser, but that required a bit of manual work. I also outlined some plans
to add built-in Prism support in RuboCop. [^1] Today I'm happy to report that last
week we've released [RuboCop 1.62](https://github.com/rubocop/rubocop/releases)
which features (experimental) support for Prism!

Now using RuboCop with Prism is just a matter of adding Prism to your
`Gemfile` (assuming you're using Bundler, that is):

``` ruby
gem 'prism'
```

and adding the following to your RuboCop configuration:

``` ruby
AllCops:
  ParserEngine: parser_prism
  TargetRubyVersion: 3.3
```

Magic! Initial benchmarks we've done suggest that using Prism results in about 70% speedup of the parsing speed,
but I'd take those with a grain of salt as we're very early in the process of optimizing RuboCop and `rubocop-ast` for
Prism, plus the results you'd get would be strongly correlated with what you're parsing.[^2] At any rate - it's pretty certain that Prism is faster than `whitequark/parser`.

Why is the Prism support considered "experimental"? There a few reasons for this:

- Prism currently supports parsing only Ruby 3.3+, compared to `whitequark/parser` supporting
Ruby 2.0+. This means Prism is still not a complete replacement for `whitequark/parser` in all use-cases.
- There are a bunch of small issues with Prism's `parser` translation that have to be fixed. In other words - for the time being this means that some cops won't be working properly with Prism. You can monitor the list of outstanding Prism issues related to RuboCop [here](https://github.com/ruby/prism/issues?q=is%3Aissue+is%3Aopen+label%3Arubocop).
- We might decide to make some changes to the configuration options. (although this seems unlikely)

I expect that as more people start to use RuboCop with Prism we'll quickly identify and fix any outstanding problems. I'd encourage the more adventurous people to play a bit with RuboCop and Prism and report their findings. (e.g. issues discovered, benchmarks, etc)

RuboCop will keep supporting `whitequark/parser` for the foreseeable future, so there's no rush for anyone to switch to using Prism today.
Also, it's not like we can stop supporting `whitequark/parser` until we switch to using Prism's AST format natively - currently we're using
Prism as a backend for `whitequark/parser`.[^3]

That's all I have for you today. Keep hacking!

[^1]: See my [previous article]({% post_url 2024-02-19-need-for-speed-using-rubocop-with-prism %}).
[^2]: See <https://github.com/rubocop/rubocop-ast/pull/277#issuecomment-1962383316>
[^3]: See <https://github.com/ruby/prism/blob/main/docs/parser_translation.md> for details.
