---
layout: post
title: 'CIDER.mx: Reloaded'
date: 2019-05-29 16:12 +0300
categories: posts
tags:
- Clojure
- CIDER
- Documentation
- AsciiDoc
- Antora
---

## docs.cider.mx

I hope that by now most CIDER users have heard of <https://docs.cider.mx>[^1]
and I have spent some quality time there. Today I'm excited to share with you a few
updates about CIDER's documentation site.[^2]

Since the site was launched it was always powered by a combination of [MkDocs](https://www.mkdocs.org/)
and [ReadTheDocs](https://readthedocs.org/). MkDocs was used to convert the documentation from Markdown to
HTML, and ReadTheDocs (RTD for short) was used to build and publish the documentation. This arrangement
worked out reasonably well, but it was also frustrating for several reasons:

* Markdown is a horrible format to write technical documentation in. A lot has been written on the subject, so
I'll just leave it at that.[^3]
* RTD doesn't support MkDocs particularly well.
* The only alternative of MkDocs supported by RTD is Sphinx, which is not that much better than MkDocs.
* RTD doesn't allow you to customize the documentation look and feel much. The default theme looks pretty
dated and the typography doesn't look great.
* You can't bind a RTD site to an apex custom domain (e.g. `cider.mx`).
* For some reason the versions of the documentation sites under `readthedocs.io` (e.g. `cider.readthedocs.io`) get much higher page ranks than the custom domain version (e.g. `docs.cider.mx`).

Generally if I have to sum it up I'll say that with RTD you're trading convenience for flexibility. That's fine for small
projects, but becomes really painful after a certain point.

I've been planning to move away from RTD for a very long time now, but I dreaded all the work this was
going to entail. Through the years several people promised to help with that migration, but that never happened. In the end I finally mustered the courage to pull the trigger on the migration over the weekend.
Recently I've had a really positive experience working with [AsciiDoc](https://asciidoctor.org/) and [Antora](https://antora.org/) on <https://nrepl.org>, so
I've decided to adopt them for CIDER's site as well.

I did the migration in a semi-automated manner. I'd convert the Markdown files with [kramdoc](https://github.com/asciidoctor/kramdown-asciidoc) and manually address
any conversion flaws in the resulting AsciiDoc files. I'd also manually build the Antora navigation index and update cross-references between the files.
It took me a couple of days, but in the end the result was quite to my liking. There are several outstanding issues with the new documentation site:

* There's no search functionality currently.[^4]
* I couldn't figure out how to hide the H1 heading on the index page (the one right above the logo).[^5]
* There might be broken links/formatting I haven't spotted during the conversion pass.
* The manual is now regenerated by hand.
* There are no "Next/Previous" links.

I'd really appreciate some help with tackling those problems!

On the bright side - with Antora now we can pull documentation modules from multiple repositories into a single
documentation site and I hope we'll make some good use of this down the road. I'd definitely love to document
better `cider-nrepl` and friends and publish their docs alongside CIDER's.

I guess some people might be surprised about the choice of the markup language - after all isn't `org` or `texinfo` a better option for
Emacs? That might very well be true, but they are too Emacs-specific and I wanted to go with something generic, as I'll be
using the same documentation pipeline for multiple project and I do hope
that non-Emacs users will find some reason to contribute documentation to some of those projects.
I did a very extensive research and nothing came close in my book to AsciiDoc. I encourage everyone to check out this
[great comparison with Markdown](https://asciidoctor.org/docs/asciidoc-vs-markdown/).

By the way, my only real gripe with AsciiDoc is ironically the Emacs support for it. Unfortunately [adoc-mode](https://github.com/sensorflo/adoc-mode)
has been dormant for years and doesn't support newer additions to AsciiDoc. Still, it gets the job done and I can live with it. Frankly speaking -
AsciiDoc is so simple that you don't really need a dedicated major mode for it (which also applies to Markdown, of course).

**Note:** I've opted to keep the legacy site running for now, as there were a ton of links to it. You can find it at <https://cider.readthedocs.io>.

Some of you will notice that the changes to the manual are not only visual. I've actually re-arranged the manual a lot and I've added and extended some sections.
Once the dust settles I'll focus my efforts completely on the quality of the documentation.

## cider.mx

While working on the new documentation site I realized that due to some technical limitation of GitHub Pages I can't have
the site mapped both to `docs.cider.mx` and the apex domain `cider.mx`. That was a bummer, but it finally forced me to
spend some time on creating a proper landing page for CIDER.[^6] You can find the new home page on <https://cider.mx>.
It's super basic and it's just one static HTML file and a bit of Bootstrap 4.[^7] This was the first page I've worked on
in a very long time and it made me realize I'm completely disconnected from modern front-end.

I hope some of you will be willing to help me make it look nicer and more professional!

I'm also trying to collect a few testimonials from happy CIDER users for the final section of the page, so I'd
appreciate it if you lend me a hand with that![^8] In general welcome all feedback and ideas about the new home page and the new documentation site!

## Epilogue

When it comes to documentation obviously the content is way more important than its appearance.
Still, I've always been optimistic that we can have great content and great appearance at the same time.
On open-source projects that's kind of hard to achieve given the limited time one can allocate to them,
but the dream is definitely alive, and I think those two site updates where a great step forward fulfilling it.

In the months to come I'll continue to polish the visuals (and I'll try to educate myself a bit on modern front-end), but
I'll also spend some time reviewing and revising all of the documentation that currently exists.
CIDER is already a solid and capable tool, but unfortunately relatively few people are aware of its full potential.
I hope those documentation efforts, together with the "Hard CIDER" blog series are finally going to address those problems.

That's all I have for you today! Keep hacking and keep documenting!

[^1]: Also aliased to <https://www.cider.mx> for historical reasons.
[^2]: I know documentation is not an exciting topic for most people, but I'm weird.
[^3]: <https://medium.com/@Mister_Gold/stop-using-markdown-for-documentation-5bda05ad17e3>
[^4]: <https://gitlab.com/antora/antora-ui-default/issues/44>
[^5]: <https://gitlab.com/antora/antora/issues/336>
[^6]: I think you'll all agree that a simple project overview is a better home page than a documentation site.
[^7]: You have no idea how much time it took to create this trivial page. Dealing with CSS is still painful, although not nearly as painful as it was 15 years ago.
[^8]: Provided those exist in the first place.