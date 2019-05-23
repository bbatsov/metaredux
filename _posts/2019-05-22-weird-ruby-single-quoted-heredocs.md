---
layout: post
title: 'Weird Ruby: Single-quoted Heredocs'
date: 2019-05-22 16:52 +0200
category: posts
tags:
- Ruby
- Weird Ruby
- Series
---

By default heredocs in Ruby behave like double-quoted strings when it comes to
interpolation and escape characters:

``` ruby
land = 'Mordor'

verse = <<-TEXT
One Ring to rule them all,
One Ring to find them,
One Ring to bring them all,
and in the darkness bind them,
In the Land of #{land} where the Shadows lie.
TEXT

# => "One Ring to rule them all,\n" +
# "One Ring to find them,\n" +
# "One Ring to bring them all,\n" +
# "and in the darkness bind them,\n" +
# "In the Land of Mordor where the Shadows lie.\n"
```

There's a way to suppress this behavior and make heredocs behave like
single-quoted strings, but it's a little bit... weird[^1]:

``` ruby
land = 'Mordor'

verse = <<-'TEXT'
One Ring to rule them all,
One Ring to find them,
One Ring to bring them all,
and in the darkness bind them,
In the Land of #{land} where the Shadows lie.
TEXT

# => "One Ring to rule them all,\n" +
# "One Ring to find them,\n" +
# "One Ring to bring them all,\n" +
# "and in the darkness bind them,\n" +
# "In the Land of \#{land} where the Shadows lie.\n"
```

This works for both normal and squiggly heredocs (`<<~`).
Notice that you have to put only the opening delimiter
within single quotes.

That's all I have for you today! Keep hacking and keep Ruby weird!

## Articles in the Series

<ul>
{% for post in site.posts reversed %}
  {% if post.tags contains 'Weird Ruby' %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endif %}  <!-- tags if -->
{% endfor %} <!-- posts for -->
</ul>

[^1]: Unless you're into Perl.
