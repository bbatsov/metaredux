---
layout: post
title: 'Need for Speed: Using RuboCop with Prism'
date: 2024-02-19 15:12 +0200
tags:
- Ruby
- RuboCop
- Prism
---

By now probably most Rubists have heard of
[Prism](https://github.com/ruby/prism) (formerly know as YARP), the modern and
(very) fast Ruby parser that's aiming to replace all the existing Ruby parsers out
there. Including RuboCop's "own" [parser](https://github.com/whitequark/parser) (a.k.a `whitequark/parser`), which
aimed to replace `ripper` and `ruby_parser`, back when it was created.

I've been keeping an eye on Prism for while, as RuboCop has long been criticized
for its choice of parser (and more specifically - for that parser being somewhat
slow). That being said - parser migrations are always a pain, especially in a
project as big as RuboCop. Early on I had to essentially rewrite RuboCop when I
switched the codebase from `ripper` to `parser`, and back then RuboCop was a lot
smaller. The good thing is that such rewrites can be done incrementally (I was
migrating cops in batches), but it was still a lot of (boring, repetitive) work.

That's why I was super happy when I recently discovered
[parser-prism](https://github.com/kddnewton/parser-prism) - a gem that provides
a new backend for the `parser` gem's syntax tree that uses the Prism parser. To
make things better - it seemed that this library actually [worked with
RuboCop already](https://github.com/kddnewton/parser-prism#rubocop). Yeah, the users
still had to do some manual work, but it turned out that the migration could be
a lot simpler than what I had expected. Did I also mention some mighty impressive
benchmarks?

----------------------------------------

As a whole, this parser should be significantly faster than the `parser` gem. The `bin/bench` script in this repository compares the performance of `Parser::CurrentRuby` and `Parser::Prism`. Running against a large file like `lib/parser/prism/compiler.rb` yields:

```
Warming up --------------------------------------
 Parser::CurrentRuby     1.000  i/100ms
       Parser::Prism     6.000  i/100ms
Calculating -------------------------------------
 Parser::CurrentRuby     16.642  (± 0.0%) i/s -     84.000  in   5.052021s
       Parser::Prism     64.951  (± 3.1%) i/s -    330.000  in   5.088147s

Comparison:
       Parser::Prism:       65.0 i/s
 Parser::CurrentRuby:       16.6 i/s - 3.90x  slower
```

When running with `--yjit`, the comparison is even more stark:

```
Warming up --------------------------------------
 Parser::CurrentRuby     1.000  i/100ms
       Parser::Prism     9.000  i/100ms
Calculating -------------------------------------
 Parser::CurrentRuby     20.062  (± 0.0%) i/s -    101.000  in   5.034389s
       Parser::Prism    112.823  (± 9.7%) i/s -    558.000  in   5.009460s

Comparison:
       Parser::Prism:      112.8 i/s
 Parser::CurrentRuby:       20.1 i/s - 5.62x  slower
```

These benchmarks were run on a single laptop without a lot of control for other processes, so take them with a grain of salt.

--------------------------------------

**Note:** The results above were taken straight from `parser-prism`'s README. In a nutshell we're looking into something like 4-6 times speedup!!! Now I was VERY excited!

Immediately I created [an
issue](https://github.com/rubocop/rubocop/issues/12600) to improve the support
for Prism in RuboCop and we've started to collaborate with Prism's author [Kevin
Newton](https://github.com/kddnewton), who has been very supportive and
accommodating. I suggest to everyone interested in the topic to peruse the
discussion in this issue (and issues references from it), as we've already
managed to simplify the usage of RuboCop with Prism quite a bit. And we're very
close to addressing the main outstanding item - [multi-versioning for the
parser](https://github.com/ruby/prism/pull/2419). Basically, the `parser` gem
allows you to select the version of Ruby to parse code as (e.g. 3.1),
independently from the version of Ruby runtime. This powers the
`TargetRubyVersion` configuration option in RuboCop and was one of the many
reasons for picking `parser` over `ripper` back in the day. In practical terms -
this allowed us to keep supporting parsing Ruby 2.x code, long after one
couldn't run RuboCop on Ruby 2.x. To put it in different terms - the versions of Ruby that
RuboCop understands (can parse) are completely orthogonal to the versions of Ruby on which
RuboCop can be run.

This and other items we need to address, are nicely summarized
[here](https://github.com/rubocop/rubocop/issues/12600#issuecomment-1882287131). None
of them seems like a particularly hard obstacle, so I'm quite optimistic about
the future. A future in which one day you'll be able to have this in your
RuboCop config:

``` yaml
ParserEngine: prism
```

When is this going to happen exactly? No promises yet, but at the current pace it
will likely happen sooner rather than later. I'd encourage adventurous
people to play with RuboCop on Prism and to contribute
to getting all required pieces in place faster.

I recall that RuboCop's adoption of `whitequar/parser` was instrumental in
uncovering (many) weird bugs in it, and I have a feeling that things might be
the same for `parser-prism` as well. (I assume a lot less projects use
`parser-prism` compared to Prism) Exciting times ahead!

That's all I have for you today! Keep hacking!
