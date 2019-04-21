---
layout: post
title: Meta Reduce, Volume 2019.1
date: 2019-04-21 10:25 +0300
categories: posts
tags:
- Clojure
- nREPL
- Orchard
- Meta
- Emacs
- CIDER
- Ruby
- RuboCop
---

Time to recap what I've been up to lately. Admittedly this post is
a bit overdue[^1], but there has been a lot on my plate
lately and I had little time for blogging.

<!--more-->

## Clojure

### Dutch Clojure Days

The most interesting thing I've done on the Clojure front recently was
speaking at the [Dutch Clojure Days
conference](https://clojuredays.org/). I spoke there about nREPL's
revival, future and importance. I think the talk went well and
resonated with the audience. I even got an unexpected promise of
financial backing for the project by [Siili](https://www.siili.com/)!

Here's the [slide
deck](https://speakerdeck.com/bbatsov/nrepl-redux-dcd-2019) from my
presentation.  I hope the recording of the talk will
become available soon.

I think it was a great conference overall and I'm happy I got the
opportunity to be a part of it. I certainly hope that I'll find my way
back there, even if I'm just a regular attendee. I have to note that even though
the conference was **completely free** it was organized really well!
You could clearly tell that it was a product of love and passion and that's the only
real recipe for a great conference!

By the way, Josh Glover recently wrote a [nice
summary](https://medium.com/@jmglov/thoughts-on-dutch-clojure-days-2019-56f70d12cefc)
of the conference.

### nREPL

There's not much to report here. The most important thing that
happened was the decision to [make op names strings
internally](https://github.com/nrepl/nrepl/pull/139). This simplified
the implementation of the new EDN transport and make the code simpler
overall.

I've also [updated the nREPL
section](https://github.com/lambdaisland/lambdaisland-guides/pull/12/files)
in Lambda Island's [guide to Clojure
REPLs](https://lambdaisland.com/guides/clojure-repls) to reflect the
recent nREPL changes.

### Orchard

On Orchard's front the big news is that we're very close to getting
rid of almost all third-party dependencies the project currently has -
namely `tools.namespace` and `java.classpath`. This happened as the
side-effect of work to address dynapath-related issues on Java 9+. You
can learn more about all of this
[here](https://github.com/clojure-emacs/orchard/issues/20). Once we
wrap this up, I'll cut Orchard 0.5 and we'll focus our attention on
Orchard 0.6 and the huge milestone of merging ClojureScript support into
Orchard (and deprecating `cljs-tooling` in the process).

Fun times ahead!

### CIDER

I've finally found a bit of time to work on CIDER.

My current focus is getting more functionality working without
`cider-nrepl`.  The biggest step in this direction was implementing an
eval-based fallback for `cider-var-info` - a function which powers
definition lookup and doc lookup (amongst others CIDER commands).  You
can read more about the scope of that task
[here](https://github.com/clojure-emacs/cider/issues/2611) and I'd
certainly appreciate some help with it.

On a related note - I can't wait for us to
implement client dependency injection in nREPL itself, as it would
simplify that functionality tremendously.

In other news - I fixed a [long-standing
problem](https://github.com/clojure-emacs/cider/issues/2308) with
checking for the presence of required ClojureScript dependencies
before starting a ClojureScript REPL, but at this point I'm thinking
that probably this whole dependency validation idea was a bad one and
I should just kill those checks completely.

I've also looked into a [compilation error highlighting
regression](https://github.com/clojure-emacs/cider/issues/2614) caused
by changes in Clojure 1.10. It's a trivial problem, but Emacs Lisp
regular expressions have a very messed up syntax that makes it really
unpleasant to work with them. Here's the regular expression in question:

``` emacs-lisp
(defvar cider-compilation-regexp
  '("\\(?:.*\\(warning, \\)\\|.*?\\(, compiling\\):(\\)\\(.*?\\):\\([[:digit:]]+\\)\\(?::\\([[:digit:]]+\\)\\)?\\(\\(?: - \\(.*\\)\\)\\|)\\)" 3 4 5 (1))
  "Specifications for matching errors and warnings in Clojure stacktraces.
See `compilation-error-regexp-alist' for help on their format.")
```

How many things do you need to escape???

## Ruby

### RubyDay

Only 5 days after DCD I did a second talk at the RubyDay conference in
Verona, Italy. I spoke about Ruby 3 there and you can check out my
slide deck [here](https://speakerdeck.com/bbatsov/ruby-3-redux).
Funny enough, [a lot was
said](https://docs.google.com/presentation/d/1z_5JT0-MJySGn6UGrtdafK1oj9kGSO5sGlTtEQJz0JU/edit#slide=id.p)
about Ruby 3 on RubyKaigi just a week after my talk. I'm pretty glad that I
was spot on with most of my predictions though, despite the limited
sources of information I was working with.

You've got no idea how stressful it is to work on two new talks for back-to-back conferences!
I was so relieved after I wrapped up my RubyDay talk! And I'm never doing this again.[^2]

Apart from the stress RubyDay was a really nice event and I had a lot of fun meeting
the local Ruby community. The conference was also a nice excuse for me
to travel around Verona and spend a week enjoying Italian food and
wines.

### RuboCop

RuboCop saw a ton of activity lately and an important [new
release](https://github.com/rubocop-hq/rubocop/releases/tag/v0.67.0),
which improved a lot its pretty-printing capabilities.

RuboCop 0.68 is already right around the corner and I'm really excited
about getting some [line length autocorrection
support](https://github.com/rubocop-hq/rubocop/pull/6927) in
it. That's going to be a massive improvement in the formatting department.

I was also quite pleased to see what [Flexport are
doing](https://flexport.engineering/approximating-prettier-for-ruby-with-rubocop-8b863bd64dc6)
to push this even further!  I'm really grateful to see them making
their work open-source and contributing it upstream!

## Haskell

I continue slowly with my Haskell explorations and I'm having quite a bit of fun overall.
Nothing interesting to report, though.

## Joe

I never met Joe Armstrong in person, but I've always admired his work
and he was a big source of inspiration for me. I was really saddened
by the news of his passing on Saturday and I want to take a moment to
honour his memory. I've often pondered on the legacy of software
engineers, as things are some transient and ephemeral in our line of
work. I have no doubt, however, that Joe's legacy will live on for a
very long time and his work will continue to inspire software engineer in
the years to come!

Rest in Peace, Joe! You'll be missed, but not forgotten!

## Real World

It was really nice to spend some time in Amsterdam and Italy around
the two conferences.  I finally managed to visit Milan and the nearby Lake
Como, after planning to do so for ages, and they didn't disappoint.  I
can also heartily recommend to everyone to spend some time in the
nearby town of Bergamo (where most low-cost airlines bound for Milan
tend to land).

I've started reading "Persepolis Rising" (the seventh book in the "The
Expanse" sci-fi series) and so far I find it to be pretty
disappointing.  Generally I've noticed that most book series really
struggle to keep their momentum past volume #3.

On the movies side I finally watched "Baby Driver" and I certainly
enjoyed it a lot. The movie's style and soundtrack are quite something
and felt very refreshing to me! Yesterday I went to see "Pet
Sematary" and it was so-so. I think I liked the first movie better
(even if I can barely remember it at this point). This was a
reminder I should probably read the book one of those days. Later
today I plan to hit the movies again with "Shazam!". Fingers crossed!

As usual, I wanted to do many other fun or healthy things, but I miserably failed.
Better luck next time!

[^1]: I was hoping I'd write one each week.
[^2]: Although I've promised this to myself in the past as well.
