---
layout: post
title: 'Weird Ruby: Mixing Code and Data'
date: 2023-12-23 10:20 +0200
tags:
- Ruby
- Weird Ruby
- Series
---

One of the fun weird aspects of Ruby is that you can mix code and data together in the same file. Here's a trivial example:

``` ruby
DATA.each_line do |line|
  puts line
end

__END__
I
am
Groot
```

If you put the snippet above in a file named `groot.rb` and run with `ruby` it will output the following:

```
I
am
Groot
```

So, what happened? Basically the `__END__` token marks the end of the Ruby code
in a source file and everything afterwards is treated as data and bound to an
special IO object named `DATA` (of type `File`).

That's one of the many weird features that Ruby inherited from Perl[^1] and is
useful when you're playing with a single-file script and you need some test
data.[^2] Not the most useful feature in a Rails application, though.

That's all I have for you today. Keep Ruby weird!

[^1]: See <https://perldoc.perl.org/perldata#Special-Literals>
[^2]: Might be pretty useful for <https://adventofcode.com/>
