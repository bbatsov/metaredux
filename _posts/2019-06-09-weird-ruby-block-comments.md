---
layout: post
title: 'Weird Ruby: Block Comments'
date: 2019-06-09 11:41 +0300
category: posts
tags:
- Ruby
- Weird Ruby
- Series
---

Welcome to another installment of "Weird Ruby"! Today we're going to be
discussing the typically boring subject of code comments. I guess pretty
much every Ruby developer knows that comments in Ruby start with `#` and
typically look something like this:

``` ruby
# I'm a comment.
```

That's not the whole story, though. Ruby actually has a second syntax for comments,
which is focused on block (multi-line) comments. Here's how it looks:

``` ruby
# I'm a plain old multi-line comment.
# You've probably seen a million like me.

=begin
I'm a block comment.
I look kind of weird and you've probably never
seen one like me before.
=end
```

So, what's so weird about this syntax that it made it to "Weird Ruby"? After all other
programming languages have the block comments as well.[^1] It's pretty simple -
the block comment syntax has the weird requirement that the comments have to
begin **at the very start** of each line. Consider this:

``` ruby
class SomeClass
=begin
This is a block comment.
It looks pretty out of place.
=end
  def some_method
  end
end

# Don't do this at home!
class SomeClass
  =begin
  This looks like a block comment, right?
  Unfortunately it's a syntax error.
  =end
  def some_method
  end
end
```

Bummer! As you can imagine this really limits the usefulness of the block comment syntax and
probably this is the reason why it never gained any traction. I've got no idea why
the syntax was designed in a such a limiting manner, or if Ruby's Core Team plans to address or remove it down the road.
If someone knows something on the subject - please, share your insight via a comment.
The good thing is that in the era of today's super powerful editors and IDEs you don't really
lose much, as they make it really easy to (un)comment blocks of code. Perhaps we no longer
need a special block comment syntax at all?

That's all I have for you today! Hopefully it was weird enough, fun and somewhat useful!
Keep hacking!

{% include series_listing.html site=site series="Weird Ruby" %}

[^1]: `/* */` from C and family immediately come to mind.
