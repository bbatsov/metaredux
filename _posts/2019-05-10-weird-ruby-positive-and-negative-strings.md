---
layout: post
title: 'Weird Ruby: Positive and Negative Strings'
category: posts
tags:
- Ruby
- Weird Ruby
- Series
---

Recently I saw the following Ruby code:

``` ruby
content = +"\n"
content << "## #{title}\n"
content << "\n"
content
```

My initial reaction was WTF! The `+` before `"\n"` seemed like some
potential typo to me. A moment later I was enlightened and I told
myself - "Wow! That's weird enough for Weird Ruby!". And here we are:

Turns out that when [frozen string
literals](https://freelancing-gods.com/2017/07/27/an-introduction-to-frozen-string-literals.html)
were introduced in Ruby 2.3 a couple of unary methods were added to
the `String` class - namely unary `+` and `-`. They made it possible
to have "positive" and "negative" string literals. What does this mean
exactly? Let's figure this out together!

## Positive Strings

Let's first take a look at `String#+@`. Here's its description from
the [official API
docs](https://ruby-doc.org/core-2.5.1/String.html#method-i-2B-40):

> If the string is frozen, then return duplicated mutable string.
> If the string is not frozen, then return the string itself.

Simple enough, right? Here are a couple of examples to help illustrate this:

``` ruby
# frozen_string_literal: true

foo = "string"
foo.upcase!
# => FrozenError - can't modify frozen String
(+foo).upcase!
# => "STRING"
```

What's the practical application of this weird notation? I think the
number one usage would to be to give you an unfrozen string that you
can use as the receiver for mutating string methods
(e.g. concatenation):

``` ruby
# frozen_string_literal: true

# Doesn't work because you can't modify a frozen string literal
content = "\n"
content << "## #{title}\n"
content << "\n"
content

# Common workaround
content = "\n".dup
content << "## #{title}\n"
content << "\n"
content

# The new method in action
content = +"\n"
content << "## #{title}\n"
content << "\n"
content

# A non-destructive alternative
content = "\n"
content += "## #{title}\n"
content += "\n"
content
```

You'll be the judge of how useful this is for you. Concatenation is
obviously more memory efficient, but in simple scenarios you don't
gain much by using it.

## Negative Strings

Now, let's turn our attention to `String#-@`. Here's its description from
the [official API
docs](https://ruby-doc.org/core-2.5.1/String.html#method-i-2D-40):

> If the string is frozen, then return the string itself.
> If the string is not frozen, return a frozen, possibly pre-existing copy of it.

And here are a few examples:

``` ruby
foo = "string"
foo.upcase!
# => "STRING"
(-foo).downcase!
# => FrozenError - can't modify frozen String
```

Generally, I don't think you'll find any use for it if you're leveraging the `frozen_string_literals` pragma, but it's
an useful shorthand to freeze a string otherwise.

## Closing Thoughts

A word of caution - the positive and negative string literals result in code that's pretty weird, so you should use them sparingly.

Until next time! Keep hacking!

## Articles in the Series

<ul>
{% for post in site.posts reversed %}
  {% if post.tags contains 'Weird Ruby' %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endif %}  <!-- tags if -->
{% endfor %} <!-- posts for -->
</ul>
