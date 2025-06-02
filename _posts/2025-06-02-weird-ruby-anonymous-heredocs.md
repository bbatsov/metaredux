---
layout: post
title: 'Weird Ruby: Anonymous Heredocs'
date: 2025-06-02 22:55 +0300
tags:
- Ruby
- Weird Ruby
- Series
---

Heredocs in Ruby are quite common, quite flexible, and... somewhat weird. Still,
this doesn't stop people from suggesting more features for them, like this
(rejected) [proposal](https://bugs.ruby-lang.org/issues/20852) for an anonymous
heredocs syntax:

```ruby
# regular Ruby code
Markdown.render <<~MARKDOWN
  # Hello there

  This is a Markdown file. See?

  1. This is a list
  2. With items
  3. And more items
MARKDOWN

# proposed syntax
Markdown.render <<~
  # Hello there

  This is a Markdown file. See?

  1. This is a list
  2. With items
  3. And more items
~>>
```

Even though this proposal didn't make it, you can get pretty close by just using `_`
as your heredoc delimiter:

```ruby
Markdown.render <<~_
  # Hello there

  This is a Markdown file. See?

  1. This is a list
  2. With items
  3. And more items
_
```

Looks a bit weird, but it gets the job done.

That's all I have for you today. Keep Ruby weird!
