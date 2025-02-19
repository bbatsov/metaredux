---
layout: post
title: Counting Lines
date: 2019-12-13 9:00 +0200
categories: posts
tags:
- Meta Advent 2019
- Command-line Tools
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

Love it or hate it, the lines of code in a project is still an useful metric from time to time.
Recently I resorted to using it again when I needed to track the progress of breaking down a monolith application
into several applications and services at work.

Historically I've been using the [sloccount](https://dwheeler.com/sloccount/) for as long as I could remember, but recently
I've decided to look for some alternatives, as `sloccount` really struggles with big projects. Not to mention
it doesn't understand many of the programming languages that became popular in recent years.

Turns out there's a shitload of new[^1] options out there:

* [loc](https://github.com/cgag/loc/)
* [gocloc](https://github.com/hhatto/gocloc/)
* [Ohcount](https://github.com/blackducksoftware/ohcount/)
* [scc](https://github.com/boyter/scc/)
* [sclc](https://code.google.com/archive/p/sclc/)
* [SLOCCount](http://www.dwheeler.com/sloccount/)
* [Sonar](http://www.sonarsource.org/)
* [tokei](https://github.com/Aaronepower/tokei/)
* [Unified Code Count](http://csse.usc.edu/ucc_new/wordpress/)

Seems people really love counting lines of code! This certainly reminded of me of
numerous `grep` clones (`ack`, `ag`, `pt`, etc.).
I was too lazy to evaluate all of the tools, but I played a bit with `cloc` and `scc`[^2] and
they get the job done well enough. Installing them on macOS is super easy if you're into
Homebrew:

```console
$ brew install cloc scc
```

Both tools work fairy similarly. Here's some data for my [RuboCop](https://github.com/rubocop-hq/rubocop) project:

```console
$ cloc .
    1264 text files.
    1264 unique files.
      13 files ignored.

github.com/AlDanial/cloc v 1.84  T=1.86 s (674.8 files/s, 111068.6 lines/s)
--------------------------------------------------------------------------------
Language                      files          blank        comment           code
--------------------------------------------------------------------------------
Ruby                           1120          24487          17837         124979
Markdown                        120           6306              0          27093
YAML                              9            460            658           3426
HTML                              1            107              0            593
ERB                               1              8              1            252
CSS                               1              1              3             11
Bourne Again Shell                1              2              0              5
--------------------------------------------------------------------------------
SUM:                           1253          31371          18499         156359
--------------------------------------------------------------------------------

$ scc
───────────────────────────────────────────────────────────────────────────────
Language                 Files     Lines   Blanks  Comments     Code Complexity
───────────────────────────────────────────────────────────────────────────────
Ruby                      1114    166605    23115     17637   125853       5544
Markdown                   120     33399     6306         0    27093          0
YAML                         9      4544      460       658     3426          0
BASH                         1         7        2         1        4          0
CSS                          1        15        1         3       11          0
Gemfile                      1        26        5         4       17          0
HTML                         1       700      107         0      593          0
Plain Text                   1        20        3         0       17          0
Rakefile                     1       129       22         5      102         11
gitignore                    1        64       14        35       15          0
───────────────────────────────────────────────────────────────────────────────
Total                     1250    205509    30035     18343   157131       5555
───────────────────────────────────────────────────────────────────────────────
Estimated Cost to Develop $5,466,008
Estimated Schedule Effort 29.255363 months
Estimated People Required 22.131939
───────────────────────────────────────────────────────────────────────────────
```

Overall the data is pretty similar.
The main differences are performance and details in the report.
`cloc` is much slower, as it's written in Perl. `scc` (written in Go)
would handle instantly any project I threw at it. You'll also notice
that `scc` features in its report some complexity data that's similar
to what you'd get from `sloccount`. `cloc` seems to
handle Ruby filetypes better, as `scc` didn't understand that `Rakefile` and `Gemfile` are
Ruby code. I'm on the fence as to whether we should count `.erb` files separately or
as HTML.

`cloc` has all sorts of fancy features like counting lines in an archive file (e.g. `zip`),
counting lines for some git revision, etc. I don't really need anything like this,
but those features might be useful to some of you.
I definitely prefer `scc` and I think I'll stick with it going forward.

So, what's your favourite line counting tool and why?

[^1]: For some definition of "new".
[^2]: I selected them because I liked their names the most.
