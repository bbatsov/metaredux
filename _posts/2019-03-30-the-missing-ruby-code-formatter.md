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
started way back in 2012.  Most people think of RuboCop as a lint
tool, but it actually has an entire department (type) of cops (code checks)
dedicated to inspecting and fixing code layout (a.k.a. code
formatting).

Having options to chose from is generally a good thing, but it also
implies that you have to make some thinking, and perhaps even a bit
of research, to make sure you've chosen the right thing.[^1] So, which
tool should you use?  As I'm the author of RuboCop it's obvious that
I'm biased. It's fair to assume by this point you're certain I'll be praising
RuboCop and extolling its virtues against the competition for the
remainder of this post. Well, I'll do my best to surprise you, at
least a bit.

<!--more-->

## Getting to Know the Contestants

To really pick the right tool for yourself, you should first understand
the trade-offs in the approaches taken by all of them.  I'm not an
expert on RuFo and friends, but I think I got the basics there right:

* Those tools (except the old `rubyfmt`) use Ruby's built-in parser called `ripper`.
* They are completely regenerating the source code from its AST in the desired (properly formatted) way.
* They aim to settle on one canonical code representation, which means they won't usually support any configuration (or a very limited configuration).
* Most of them pretty-print data structures.
* They aim to be pretty fast, as they are supposed to run every time you save a file in your editor.
* Their work is typically non-transparent (you don't really see what they are about to change for you, you just assume they'll do the right thing).

RuboCop is different in some ways:

* It uses a third-party parser named `parser`.[^2]
* It doesn't regenerate the source files when it alters their layout - it only updates the parts of the files that need to be changed.
* It embraces the fact that 25 years after Ruby's creation it's unlikely to settle on one unified coding standard, so it's extremely configurable.
* It doesn't pretty-print data structures (yet).
* It is reasonably fast, but probably not as fast as something using `ripper` and not applying incremental edits to the code.
* Provides a lot of feedback to the user - in an editor you'd see messages for all places where the layout is "broken" and you'd also get explicit feedback about which parts of it were updated by RuboCop.
* It is not only a formatter - it's a generic framework for static code analysis, and layout happens to be just one type of analysis you can have.

What does all of this mean in practice? Well, let me try to break it down for you.

## The Impact of the Parser

First thing's first - let's start with the choice of the underlying parsing library. There are several options around, but in practice
almost always it boils down to one of two - the built-in `ripper` or the third-party `parser`.

### ripper

Ruby has been part of MRI since version 1.9. Many people consider it the canonical Ruby parser (for obvious reasons).
If you opt to use `ripper` you can potentially have a formatting tool with 0 runtime dependencies. That's the Holy Grail of
library/tool development! There are many other benefits to using it:

* It's (very) fast.
* It's maintained by the Ruby Core Team, which means it's unlikely that it will ever be abandoned.
* It's based on MRI's own parser, which means it's likely to be correct.

But it's not just rainbows and unicorns... RuboCop used to use `ripper` until version 0.8 and I've spent a lot of quality time with
`ripper`. Eventually I became extremely frustrated with it, because of things like:

* It has (had?) almost no documentation.
* The AST is represented just as nested arrays, which makes it very painful to work with it (unless you build some AST wrapper library around this). Ruby 2.6 added some AST library based on `ripper`, so maybe the situation here is better now.
* It used to be MRI specific back in the day. Eventually I think it made its way to JRuby as well. It's certainly not very portable when it comes to Ruby implementations.
* If you encounter a bug you're at the mercy of the Ruby Core Team to fix (or merge a fix) it for you. Depending on your luck some changes might take a very long time.
* There's almost no metadata attached to the AST node (making it hard to do something like problem location reporting).

Here's how ripper's AST representation looks like:

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

Using a bit more complex code results in a pretty intimidating AST:

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
interactions with Ripper, but I never really check it out.  Still, I
was quite disappointed with the way the new AST module was
introduced, as to my knowledge the Ruby Core Team didn't really engage
in conversations with any of the maintainers of tools that make heavy
use of such functionality and could have provided them with valuable design
feedback. I definitely believe that to solve someone's problem you should get the
person with the problem involved in the solution.

Fortunately for me I haven't had any dealings with Ripper
in a while and I don't plan to ever touch it. Enter `parser`...

### parser

Apart from its somewhat bland name, `parser` is a pretty awesome project in many regards:

* It's fast (although not quite as fast as `ripper`)
* It's very well documented.
* It's portable (works with every major Ruby implementation).
* Its AST format is very simple and very easy to work with.
* The AST has a lot of metadata attached to it.
* Parser bundles a module that allows you to easily rewrite the underlying source files (which is the basis
of RuboCop's famous auto-correct feature).

Here's how parser's AST looks like:

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
code, where are its braces, etc):

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
runtime.

Perhaps the only real problem with Parser is that ever since its
original author stopped working actively on the project there has been
a bit of stagnation there, and hopefully my post will help somewhat with
that and attract more people to work on `parser`. After all the
project needs to constantly track the upstream Ruby changes, so it can
never be "done" and will always benefit from ongoing maintenance.

The other downsides compared to ripper are the speed (although the
difference is not major) and the fact that `parser` is a third-party
dependency that you need to add to your projects.

To conclude this section I'd like to appeal to fellow formatter tool maintainers to check out `parser` and consider
giving it a shot. Initially I was very skeptical about it, but today I'm a true believer!

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

I hope you get the general idea. Ruby built-in `pp` and `ap` libraries are good examples of pretty-printers.

Generally pretty-printing is more useful for REPLs than for source
files (as you're much more likely to be dealing with big data
structures at run time), but it's definitely very cool to be able to
reformat some ugly one-line hash or array literal in your code (or
some method signature for that matter).

In this regard certainly RuboCop is way behind the competition,
although implementhing this should not be very complex. We have to decide
first, however, how far do we want to go in the layout department.[^3]

## Editor Integration

Ultimately most of the time we don't interact with formatters
directly - we do some from our favourite editor[^4] or IDE. Designing
a code formatter with ease of editor integration is essential.

Once aspect of using external code formatters that can be really
annoying is when your editor wouldn't indent the code the same way the
external tool would.  Sure, you can train yourself to never expect
anything meaningful for a command like "Indent Line", but that would
suck, wouldn't it? It would be really nice if code formatters provided
some API for editors, so that they could use the information from the
formatter to do things like indenting lines, regions of lines and
entire files.  Right now most formatters integrate themselves simply
via "on save" hooks, but we can do so much more. Of course, that would
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
something meaningful with your code.

More about this in the next section.

## One Code Style to Rule Them All

I think that adopting an universal formatter/code style 25 years into
the existence of a language is unlikely to (fully) succeed if it’s not
driven from the top (Matz). There will always be strong opposition to
whatever the tool authors decide, as people have built strong
preferences by this point in time, and they’ll need extremely compelling
arguments to change them. Change is hard, and no one really wants to
deal with it, especially if they don’t have to.

`gofmt` succeeded mostly because it was pushed from the top, pushed
from the start and everyone was expected to use it. I’m reasonably
sure this ship has failed for Ruby. I don’t know how successful the
similar projects for other languages are. I know only that Prettier is
quite successful, but it’s also configurable to some extent.

That’s why I think that the only way for a tool to gain much traction
would be if it’s aiming to enforce project consistency and allows
people to do something relatively close to what they are doing
currently.

I agree in principle with the merits of the idea of unified coding
style and formatting tools without configuration options, but I doubt
that’s feasible in practice. When I started work on RuboCop many years
ago, it wasn’t configurable and many people were outraged by this. The
Ruby community was about 15 years old at the time, many formatting patterns
existed and very few people cared about global code consistency - most
cared about getting consistency in their projects, and of course -
with their own style preferences. Making RuboCop configurable was
instrumental to its wide adoption in the Ruby community - we never got
complete alignment in the style department, but we got some alignment
and this definitely beats none.

I know some people are still aiming for full alignment and maybe
they’ll succeed at some point, but I lost my desire to participate
actively in this, as I’m tired of endless debate over trivial matters.
At this point I think I've embraced the fact that much of Ruby's charm
stems from the fact it gives you many options for everything and relies
on you to pick whatever makes you happy. Maybe that's how Matz understands
"optimizing for happiness".

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
nuances of the code I'm writing.  I could obviously stick to only one
style, but I don't see much (any) value in doing so. That's why many
RuboCop checks are a bit lax in what exactly they are enforcing.

In general I've been thinking that probably we should allow RuboCop
linters to allow simultaneously several styles down the road, and
simply to favour one of them when doing auto-corrections.

## Epilogue

Wow! I wrote an entire novel on a topic I consider to be pretty trivial!

So, what's the take away from all of this? For me the most important
thing is that when it comes to formatting, the Ruby programmers today
enjoy quite a lot of options already. Probably more than most of them
realize. It seems to me that there's a lot of overlap in the goals of
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
lessons about working with `ripper` and building static code analysis
tools that I learned the hard way.

What prompted me to write this article was the recent announcement of
`rubyfmt`. I recall that the first thought that came to my mind was
"Another one?".  How many Ruby code formatters to we need before it's
enough? When will agree to mark the code formatting as a solved problem?

We can compete, or we can work together towards the common
goal.  In my experience collaboration yields better results than
competition (at least with respect to open-source software projects).
On this positive note I'll wrap it up.

Keep hacking, everyone! May your Ruby code be always perfectly formatted and may RuboCop
be merciful towards your CI builds!

[^1]: One has to appreciate the irony - after all this formatter tools should spare from having to make certain decisions.
[^2]: I know, I know - naming is hard!
[^3]: Pun intended.
[^4]: Emacs!
