---
layout: post
title: Toward Awesome Clojure Documentation
date: 2019-04-26 17:08 +0300
categories: posts
tags:
- Clojure
- Documentation
---

There are plenty of things about Clojure that are pretty awesome, but
API documentation has historically not been one of them. Don't get me
wrong - there are plenty of Clojure projects with excellent API docs,
but they are more of an exception than the norm. In this post I'll
share some thoughts on how we ended where we are today and some ideas
about improving the status quo. The post is a re-hash of a
presentation of the same name I did a couple of years ago at ClojuTRE,
but honestly speaking I don't remember my presentation at all, so it's
highly likely that I won't cover exactly the same ground as I did in
the presentation. The core premise remains the same, though - we need
more rigid conventions for documenting APIs.

## Awesome Docstrings

Docstrings are the primary way to document Clojure code. Many
definition forms (e.g. def, defn, defmacro, ns) support docstrings and
usually it's a good idea to make good use of them, regardless of
whether the var in question is something public or private.

Unfortunately there aren't really strong established conventions about the
exact structure of docstrings, which means that their quality is typically
all over the place. Here's typical docstring:

There are several problems with it:

* It's hard to spot references to params.

Can we do better? I'm pretty certain that we can. We don't even have to invent anything new - we just have to see what other programming languages have been doing in this department. I think Common Lisp and Emacs Lisp are great examples of languages which
forced programmers to write very structured and useful docstring. Here's an example:

We can't really carbon copy those conventions to Clojure, but we can certainly borrow something from them and maybe even improve them in the process (kind of what Rich did with respect to older Lisps and Clojure).

For one - I think we can use Markdown in docstrings to highlight param and var references.

I also think it's pretty easy to adopt a convention of using gramatically correct sentences as the building blocks of docstrings. In particular - I believe it's quite important to have each docstring start a signle sentence overview. That's great for humans and it's great for programmings tools as well.

## Awesome Metadata

Metadata is one of Clojure's little touches of brilliance, that's often
overlooked. Adding the right kind of metadata can really enhance the
documentation immensely, as this data can easily be processed by development tools
and documentation engines.

There are a couple of somewhat popular conventions when it comes to using metadata for documentation purposes already:

* `:added` is widely used to signal when something was added to a library
* `:deprecated`

I think we can way more, though. A couple of things that immediately come to mind are:

* `:see-also` - one or more related vars
* `:superseded-by` - used in conjunction with `:deprecated`
* `:supercsdes`

I've also campaigned for years for specifying the indentation of complex macros via
indentation metadata.

## Beyond API Documentation

Any reasonably complex Clojure project needs extra documentation to supplant the
basic API docs. Historically everyone was doing their own thing in that area, but
lately it seems that the community has united around [cljdoc](), which makes it
really easy to publish a combination of API and additional documentation together.

I love cljdoc and I could rave about it all day long!

One of the really great things about it is that it allows you write
your docs using AsciiDoc out of the box. I know AsciiDoc is not nearly
as popular as Markdown, but it's without a doubt a better format for
technical documentation.

## Closing Thoughts

There are obviously other aspects of documentation that we didn't touch upon in this article - tests, specs, etc. And there's also the camp saying that if the code is properly written it needs no documentation at all...

There's this old perception that programmers hate writing
documentation. Bad programmers certainly do, but you're not one of
those right?

Clojure's documentation ecosystem has been slowly evolving and
maturing and has recently reached a point where creating great
documentation has become way easier and lots of fun. Now let's go have some fun
and improve the documentation of our favourite Clojure projects!

Keep hacking! And keep documenting!
