---
layout: post
title: The Missing Ruby Code Formatter
date: 2019-03-30 10:17 +0200
categories: posts
tags:
- Ruby
- RuboCop
- Code Formatting
---

Ever since Go made popular the idea of having one canonical way of
formatting the code for a particular programming language (by bundling
the `gofmt` tool with the language), programmers in every community have been
trying (with various degree of success) to replicate this.
JavaScript's [Prettier](https://prettier.io/) became a huge hit and is
very widely used today.  Elixir 1.6 bundled a standard formatter out of
the box. Many similar initiatives are underway.

In the Ruby community the quest for the One True Formatter has been
going on for quite a while and at this point there are already several
projects to chose from:

* [RuFo](https://github.com/ruby-formatter/rufo)
* [prettier-ruby](https://github.com/prettier/plugin-ruby)
* [rubyfmt](https://github.com/wojtekmach/rubyfmt) (seemingly abandoned at this point)
* [rubyfmt](https://github.com/samphippen/rubyfmt) (brand new, unrelated to the older project with the same name)

And, of course, last but (hopefully) not least, there's
[RuboCop](https://github.com/rubocop-hq/rubocop) - a project that I've
started way back in 2012.  Most people probably think of RuboCop as a
lint tool only, but it actually has an entire department (type) of cops
(code checks) dedicated to inspecting and fixing code layout
(a.k.a. code formatting).

Having options to chose from is generally a good thing, but it also
implies that you have to make some thinking, and perhaps even a bit of
research, to make sure you've chosen the right thing.[^1] So, which
code formatting tool should you use?  Have we solved by this point the
problem of Ruby's missing code formatter?

As I'm the author of RuboCop it's obvious that I'm biased. It's fair
for you to assume that I'll be praising RuboCop and extolling its
virtues and advantages over the competition for the remainder of
this post. Well, I'll do my best to surprise you, at least a bit.

<!--more-->

## Getting to Know the Contestants

To really pick the right tool for yourself, you should first understand
the trade-offs in the approaches taken by all of them.  I'm not an
expert on RuFo and friends, but I think I got the basics right:

* Those tools (except the old `rubyfmt`) use Ruby's built-in parser called Ripper.
* They are completely regenerating the source code from its [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree) in the desired (properly formatted) way.
* They aim to settle on one canonical code representation, which means they won't usually support any configuration (or a very limited configuration).
* Most of them pretty-print data structures.
* They aim to be pretty fast, as they are supposed to run every time you save a file in your editor.
* Their work is typically non-transparent (you don't really see what they are about to change for you, you just assume they'll do the right thing).

RuboCop is different in some ways:

* It uses a third-party parser named `parser`.[^2]
* It doesn't regenerate the source files when it alters their layout - it only updates the parts of the files that need to be changed.
* It embraces the fact that 25 years after Ruby's creation it's unlikely to settle on one unified coding standard, so it's extremely configurable. More about this later.
* It doesn't pretty-print data structures (yet).
* It is reasonably fast, but probably not as fast as something using Ripper and not applying incremental edits to the code.
* Provides a lot of feedback to the user - in an editor you'd see messages for all places where the layout is "broken" and you'd also get explicit feedback about which parts of it were updated by RuboCop.
* It is not only a formatter - it's a generic framework for static code analysis, and layout happens to be just one type of analysis you can have.

What does all of this mean in practice? Well, let me try to break it down for you.

## General Approach

The two general approaches you can employ to format code are basically:

* Build the code's AST and regenerate the formatted code straight from its AST (that's what all tools except RuboCop do).
* Use a mapping between the AST and the source file to update selectively parts of the existing file.

Definitely the first approach is simpler and faster to implement. The
only small downside with it are (code) comments. Comments don't appear
in the AST representation of the code, so if you want to preserve them
you might need to make some special provisions (e.g. factor in some
data obtained for a lexer).

RuboCop's approach is tricky because you need to marshal (serialize,
apply one by one) changes to the files you're correcting, so you
wouldn't create conflicts between the changes you're applying. That
strategy also comes with a certain performance cost associated with it. Not
to mention you have to be really carefully with boundaries of the code
you're updating and to factor that your changes might introduce new
formatting issues. While that approach is certainly not ideal if you're
interested only in code formatting, it does fit very well in RuboCop's
broader lint scope.

If I was building a dedicated formatter I'd certainly go with the first approach.

## Pretty-printing

Pretty-printing is pretty awesome and I think it's an important
feature of every code formatting tool. Its definition might vary, but normally it would pretty up
multi-line composite literals like arrays, hashes and the like. Here's an example:

``` ruby
# without pretty-printing
{first_name: "Bruce", last_name: "Wayne", address: {city: "Gotham", street: "Wayne Drive"}, secret_identity: "Batman"}

# with pretty-printing
{first_name: "Bruce",
 last_name: "Wayne",
 address: {city: "Gotham",
           street: "Wayne Drive"},
 secret_identity: "Batman"}
```

I hope you get the general idea. Ruby built-in `pp` and `ap` libraries
are good examples of pretty-printers.

Generally pretty-printing is more useful for REPLs than for source
files (as you're much more likely to be dealing with big data
structures at run time), but it's definitely very cool to be able to
reformat some ugly one-line hash or array literal in your code (or
some method signature for that matter).

When it comes to pretty-printing certainly RuboCop is way behind the competition,
although implementhing this should not be very complex. We have to decide
first, however, how far do we want to go in the layout department.[^3]

**Update (2019-04-04):** RuboCop 0.67 gained a limited form of
pretty-printing for arrays, hashes and method arguments. See [this
pull request](https://github.com/rubocop-hq/rubocop/pull/6824) for
details. Keep in mind that the relevant cops are **disabled** by default.

## Editor Integration

Ultimately most of the time we don't interact with formatters
directly - we do so from our favourite editor[^4] or IDE. Designing
a code formatter with ease of editor integration is essential.

Once aspect of using external code formatters that can be really
annoying is when your editor wouldn't indent the code the same way the
external tool would.  Sure, you can train yourself to never expect
anything meaningful for a command like "Indent Line", but that would
suck, wouldn't it? It would be really nice if code formatters provided
some API for editors, so that they could use the information from the
formatter to do things like indenting lines, regions of lines and
entire files.  Right now most formatters integrate themselves simply
via "on file save" hooks, but we can do so much more. Of course, that would
require some help from the editors as well. I don't think any of the existing
Ruby formatters integrate particularly well with editor indentation engines,
but that's a global problem of external formatters.

Another useful form of integration is for the formatter to be able to
provide some useful information for the editor, so that you could see some
helpful messages about problems that exists in your code, without having
to leave it.  I'll refer to this as "editor feedback". It's basically
RuboCop's console output embedded in your editor (location of the
problematic code, description of the problem, etc).

RuboCop offense reports provide exactly such type of information (in many formats):

```
test.rb:4:5: W: Layout/EndAlignment: end at 4, 4 is not aligned with if at 2, 2.
    end
    ^^^
```

This information makes it obvious to you what the formatting tool has
flagged and intends to fix.  It's also an opportunity for you to
disagree with the formatter and try to alter its behaviour somehow
(e.g. via configuration). If a tool is focused solely on reformatting
the code than this reporting/editor feedback part is kind of
optional, but then you have to rely completely on the formatter to do
something meaningful with your code. More about this in the next section.

RuboCop's design certainly lends itself very to providing good editor feedback and
in my opinion it's a clear winner when it comes to this.

## Interlude - One Code Style to Rule Them All

At this point I'd like to tackle the general premise of having one true
code layout style for a programming language like Ruby. One with long history of
giving people the freedom to do things the way they like to, and one with
a pretty complex syntax and numerous ways to format pretty much everything.

I think that adopting an universal formatter/code style 25 years into
the existence of a language is unlikely to (fully) succeed if it’s not
driven from the top (Matz). There will always be strong opposition to
whatever the formatter tool authors decide, as people have built strong
preferences by this point in time, and they’ll need extremely compelling
arguments to change them. Change is hard, and no one really wants to
deal with it, especially if they don’t have to.

`gofmt` succeeded mostly because it was pushed from the top, pushed
from the start and everyone was expected to use it. I’m reasonably
sure this ship has failed for Ruby. I don’t know how successful the
similar projects for other languages are. I've heard of a few success stories,
but I've also seems plenty of flops. Some of you probably know for my passion
for Clojure and there we're still nowhere on the universal formatting even though
the language is much younger and its syntax is infinitely simpler.

That’s why I think that the only way for a tool to gain much traction
would be if it’s aiming to enforce project consistency and allows
people to do something relatively close to what they are doing
currently.

I agree in principle with the merits of the idea of unified coding
style and formatting tools without configuration options, but I doubt
that’s feasible in (Ruby's) reality. When I started to work on RuboCop
many years ago, it wasn’t configurable (it just enforced the
[Community Ruby Style
Guide](https://github.com/rubocop-hq/ruby-style-guide)) and many
people were outraged by this. The Ruby community was about 17 years
old at the time, many formatting patterns existed and very few people
cared about global code consistency. What I noticed what that most
people cared about getting consistency in their projects, and of
course - with their own style preferences. Making RuboCop configurable
was instrumental to its wide adoption in the Ruby community - we never
got complete alignment in the style department, but we got some
alignment and this definitely beats none.

There's another thing that bothers me when it comes to unifying
formatting - often several styles work just fine and there's no real
reason to push for only one. Here's a favourite example of mine:

``` ruby
def send_mail(source)
  Mailer.deliver(to: 'bob@example.com',
                 from: 'us@example.com',
                 subject: 'Important message',
                 body: source.text)
end

def send_mail(source)
  Mailer.deliver(
    to: 'bob@example.com',
    from: 'us@example.com',
    subject: 'Important message',
    body: source.text
  )
end
```

I use those style interchangeable depending on my mood and some
nuances of the code I'm writing (e.g. how long a line of code is).  I
could obviously stick to only one style, but I don't see much (any)
value in doing so. That's why many RuboCop checks are a bit lax in
what exactly they are enforcing.

In general I've been thinking that probably we should allow RuboCop
linters to allow simultaneously several styles down the road, and
simply to favour one of them when doing auto-corrections.

I see some people are still aiming for full alignment and maybe
they’ll succeed at some point, but I lost my desire to participate
actively in this, as I’m tired of endless debate over trivial matters.
At this point I think I've embraced the fact that much of Ruby's charm
and magic stems from the fact it gives you many options for everything
and relies on you to pick whatever makes you happy. Maybe that's how
Matz understands "optimizing for happiness".

## The Impact of the Parser

Now things are about to get very technical, so feel free to skim over this section.
It's time to discuss the choice of the underlying parsing library. There are several options around, but in practice
almost always it boils down to one of two - the built-in Ripper or the third-party `parser`.

The impact of the chosen parser for the users generally affects performance, portability and UX. For the tool authors it can really impact their ability to evolve the codebase.

### Ripper

Ripper has been part of MRI since version 1.9. Many people consider it the canonical Ruby parser (for obvious reasons).
If you opt to use Ripper you can potentially have a formatting tool with 0 runtime dependencies. That's the Holy Grail of
library/tool development! There are some other benefits to using it:

* It's (very) fast.
* It's maintained by the Ruby Core Team, which means it's unlikely that it will ever be abandoned.
* It's based on MRI's own parser, which means it's likely to be correct.

But there are also quite a few downside to it as well. RuboCop used to use Ripper until version 0.8 and I've spent a lot of quality time with
it. Eventually I became extremely frustrated with it, because of things like:

* It has (had?) almost no documentation.
* The AST is represented as plain nested arrays, which makes it very painful to work with it (unless you build some AST wrapper library around this). Ruby 2.6 added some AST library based on Ripper, so maybe the situation here is better now.
* It used to be MRI-specific back in the day. Eventually I think it made its way to JRuby as well. It's certainly not very portable when it comes to Ruby implementations.
* If you encounter a bug you're at the mercy of the Ruby Core Team to fix (or merge a fix) it for you. Depending on your luck some changes might take a very long time.
* There's almost no metadata attached to the AST nodes (making it hard to do something like problem location reporting).
* The AST is more complex than it needs to be - e.g. similar types of nodes are represented by different types (e.g. prefix and postfix conditionals), language keywords are treated in a weird manner, etc. I'll ask you just to take my word for this.
* Ripper is mostly written in C, making it hard for most Rubyists to work on its codebase.

Here's how Ripper's AST representation looks like:

``` ruby
Ripper.sexp "puts 'Hello, world!'"

# AST
[:program,
 [[:command,
   [:@ident, "puts", [1, 0]],
   [:args_add_block, [[:string_literal, [:string_content, [:@tstring_content, "Hello, world!", [1, 6]]]]], false]]]]
```

Notice how all the information is simply encoded in nested
arrays. Location metadata (e.g. `[1, 0]`) is available only for the
starting location of identifiers/literals/etc.

Parsing a bit more complex code results in a pretty intimidating AST:

``` ruby
Ripper.sexp('def hello(world) "Hello, #{world}!"; end')

[:program,
 [[:def,
   [:@ident, "hello", [1, 4]],
   [:paren,
    [:params, [[:@ident, "world", [1, 10]]], nil, nil, nil, nil, nil, nil]],
   [:bodystmt,
    [[:string_literal,
      [:string_content,
       [:@tstring_content, "Hello, ", [1, 18]],
       [:string_embexpr, [[:var_ref, [:@ident, "world", [1, 27]]]]],
       [:@tstring_content, "!", [1, 33]]]]],
    nil,
    nil,
    nil]]]]
```

So scary! And so many `nil`s!
But to really understand how bad it is to work with such an API consider a bit of code from a very early version of RuboCop:

``` ruby
each(:method_add_arg, sexp) do |s|
  next if s[1][0] != :call

  receiver = s[1][1][1]
  method_name = s[1][3][1]

  if receiver && receiver[1] == 'Array' &&
     method_name == 'new' && s[2] == [:arg_paren, nil]
    offences.delete(Offence.new(:convention,
                                receiver[2].lineno,
                                ERROR_MESSAGE))
    add_offence(:convention,
                receiver[2].lineno,
                ERROR_MESSAGE)
  end
end
```

The first few lines are insane, right? I'd argue that while nested arrays might be a decent data format for
an AST, but are a very poor API.

Probably the experimental AST library added in 2.6 improved the
interactions with Ripper, but I never really checked it out.  Still, I
was quite disappointed with the way the new AST library was
introduced, as to my knowledge the Ruby Core Team didn't really engage
in conversations with any of the maintainers of tools that make heavy
use of such functionality and could have provided them with valuable design
feedback. I definitely believe that to solve someone's problem you should get the
person with the problem involved in the solution.

Fortunately for me I haven't had any dealings with Ripper
in a while and I don't plan to ever touch it. Enter `parser`...

### Parser

Apart from its somewhat bland name, `parser` is a pretty awesome project in many regards:

* It's fast (although not quite as fast as Ripper)
* It's very well documented.
* It's portable (works with every major Ruby implementation).
* Its AST format is very simple and very easy to work with.
* The AST has a lot of metadata attached to it.
* Parser bundles a module that allows you to easily rewrite the underlying source files (which is the basis
of RuboCop's famous auto-correct feature).
* It's written mostly in Ruby (it also makes use of Ragel), which makes it pretty approachable to Rubyists.

Here's how `parser`'s AST looks like:

```ruby
# Basic example
Parser::CurrentRuby.parse("puts 'Hello, world!'")

s(:send, nil, :puts,
  s(:str, "Hello, world!"))

# A "complex" example
Parser::CurrentRuby.parse('def hello(world) "Hello, #{world}!"; end')

s(:def, :hello,
  s(:args,
    s(:arg, :world)),
  s(:dstr,
    s(:str, "Hello, "),
    s(:begin,
      s(:lvar, :world)),
    s(:str, "!")))
```

I might be crazy, but it seems to me that's much easier to understand
than Ripper's output - just a bunch of nested sexp which abstract away
the information that doesn't directly pertain to the structure of the
AST.  It gets even better when we need to get some location metadata
about the elements of the AST (e.g. where does something appear in the
code, where its braces are, etc):

``` ruby
Parser::CurrentRuby.parse("puts 'Hello, world!'").loc

=> #<Parser::Source::Map::Send:0x00007fa59fc50d68
 @begin=nil,
 @dot=nil,
 @end=nil,
 @expression=#<Parser::Source::Range (string) 0...20>,
 @node=s(:send, nil, :puts,
  s(:str, "Hello, world!")),
 @selector=#<Parser::Source::Range (string) 0...4>>
```

I won't go into lengthy explanations here, as that's beyond the scope of this
article. Very simply put - here you have all the information you
could possible need wrapped in a very easy to use API. And all of this is
very well documented.

There are other nice advantages to `parser` (at least from my perspective). For one, as it's not
part of Ruby itself it can evolve at a faster pace and bugfixes can be shipped more often. That's
really important for tool writers. It's also significantly easier to contribute to `parser`, than
it is to contribute to Ruby (both in terms of process and codebase).

Another big thing are the multiple parsing modes available in Parser
(e.g. Ruby 2.0, 2.1, 2.2, etc). This allows you pick a parser
regardless of the Ruby version you're currently running on. This way
you can check Ruby syntax with a parser for Ruby 2.3, while running on
2.6. It's super handy (especially for library maintainers) and it's
not something you can do with ripper, as it's tied to the current Ruby
runtime. As a concrete example - that's the basis for RuboCop's
`TargetRubyVersion` feature.

Perhaps the only real problem with Parser is that ever since its
original author stopped working actively on the project there has been
a bit of stagnation (although a new maintainer has been pretty active
recently). I hope my post will help somewhat with that and attract
more people to work on `parser`. After all the project needs to
constantly track the upstream Ruby changes, so it can never be "done"
and will always benefit from ongoing maintenance.

The other downsides compared to Ripper are the speed (although the
difference is not major) and the fact that `parser` is a third-party
dependency that you need to add to your projects.

To conclude this section I'd like to appeal to fellow formatter tool
maintainers to check out `parser` and consider giving it a
shot. Initially I was very skeptical about it, but today I'm a true
believer! It might surprise pleasantly some of them as well and
simplify their lives and improve the experience for their users.

## And The Winner Is...

Well, it really depends... All of the formatters come with certain
trade-offs and ultimately you have to decide what do you value to the
most.

`rubyfmt` is extremely simple - just a single Ruby script, there's not even a gem.
Here certainly it makes sense to suffer working with Ripper to achieve such a simple
installation procedure. I'm also impressed that it achieves under 50 ms to run on most
source files which is definitely no small feat! On the other hand the project is young
and immature right now, and very light on the documentation. I couldn't even figure out
what are the exact formatting rules without consulting the code.

RuFo feels like a more mature, polished and flexible version of
`rubyfmt` - the project is distributed as a classic gem, supports a
certain degree of configuration and it tries to be unobtrusive by
default (make minimal changes to your code's layout). Not sure how the Ripper usage is going for them,
but probably they could investigate `parser` if it meets their performance criteria.

`prettier-ruby` relies on Prettier and I think that'd be a hard sell to people who avoid
dealing with Node.js (even though that's becoming pretty hard these day in the world of
web development). If you already use Prettier, I think going in this direction makes a lot of sense.

And in the end we have RuboCop - the only tools in the bunch that's not a dedicated formatter.
I think its main strength is that you're probably using it already, so if you're happy with what it does,
you save yourself the trouble of dealing with an extra tool. On the other hand, currently it won't
do as much as the other formatters would do, and it wouldn't do it as fast.
RuboCop's deep editor integration is definitely a big point in its favour, as is the fact that it's
a project with a pretty big community around and is likely to be maintained in the years to come.
RuboCop offers a massive degree of configuration with respect to layout, which can be both a
feature and a problem, depending on your respective. It's also the only tools which can give
you reasonable feedback about problems with your layout without fixing them. I didn't mention this earlier, but if you
want to limit RuboCop to formatting duties only you should take note of those two commands:

``` shellsession
$ rubocop --only Layout
$ rubocop --fix-layout
```

No clear winner, as far as I'm concerned, but my personal preference would be RuboCop or RuFo.
If you decide to combine RuboCop with a dedicated formatting tool you'd probably want to disable
RuboCop's layout department completely, so the two tools won't step on each other's toes.

## Epilogue

Wow! I wrote an entire novel on a topic I consider to be pretty trivial!

So, what's the take away from all of this? For me the most important
thing is that when it comes to formatting, the Ruby programmers today
enjoy quite a lot of options already. Probably more than most of them
realize. It seems to me that there's some degree of overlap in the goals of
most projects, so they might do well to join forces on
one level or another to avoid re-inventing the wheel.

I myself have been thinking about the degree to which I want us to
keep investing in formatting in RuboCop given all the alternatives
that exist today. Perhaps we should push forward on that front, so
people would have to deal with only one tool. Perhaps we should reduce
the scope of the Layout department or even remove it at some
point. Time will tell.

This article is not so much about the formatting tools themselves, but
more about the idea of the viability of unified formatting style and
the dangers of reinventing the wheel. It's also about sharing some
lessons about working with Ripper and building static code analysis
tools that I learned the hard way.

What prompted me to write this article was the recent announcement of
`rubyfmt`. I recall that the first thought that came to my mind was
"Another one?".  How many Ruby code formatters to we need before it's
enough? When will agree to mark the code formatting as a solved problem?
Don't know about you, but I definitely feel that Ruby's elusive missing code formatter
is not that missing anymore.

We can compete, or we can work together towards the common
goal.  In my experience collaboration yields better results than
competition (at least with respect to open-source software projects).
And we don't really have to collaborate on the code level only - just working towards establishing
a reasonable common formatting style would be a big step forward for everyone.
On this positive note I'll wrap it up.

Keep hacking, everyone! May your Ruby code be always perfectly formatted and may RuboCop
be merciful towards your CI builds!

[^1]: One has to appreciate the irony - after all this formatter tools should spare from having to make certain decisions.
[^2]: I know, I know - naming is hard!
[^3]: Pun intended.
[^4]: Emacs!
