---
layout: post
title: 'Weird Ruby: Incrementing Strings'
date: 2024-03-05 09:24 +0200
tags:
- Ruby
- Weird Ruby
- Series
---

I guess most Rubyists know that you can use the methods `Integer#succ`[^1] and its alias
`Integer#next` to increment a number. E.g.:

``` ruby
1.succ
# => 2
```

The method is rarely used directly, but it's used internally by ranges to compute the elements within the range boundaries:

``` ruby
(1..10).to_a
# => [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

It's not so widely known that strings also implement `succ`. The official docs describe `String#succ` like this:

> Returns the successor to str. The successor is calculated by incrementing characters starting from the rightmost alphanumeric (or the rightmost character if there are no alphanumerics) in the string. Incrementing a digit always results in another digit, and incrementing a letter results in another letter of the same case. Incrementing nonalphanumerics uses the underlying character set’s collating sequence.
>
> If the increment generates a “carry,” the character to the left of it is incremented. This process repeats until there is no carry, adding an additional character if necessary.

In more practical terms:

```ruby
"abcd".succ        #=> "abce"
"THX1138".succ     #=> "THX1139"
"<<koala>>".succ   #=> "<<koalb>>"
"1999zzz".succ     #=> "2000aaa"
"ZZZ9999".succ     #=> "AAAA0000"
"***".succ         #=> "**+"
```

Cool stuff, right? I know what you're thinking now - that'd be really handy for working with numbers represented as strings! Especially version strings! I'm sad to break it to you, but that's probably not a very good idea. Just take a look at the following example:

``` ruby
# looking good
('1.0'..'1.5').to_a
# => ["1.0", "1.1", "1.2", "1.3", "1.4", "1.5"]

# oh, shit!
('1.0'..'1.10').to_a
# => ["1.0",
#  "1.1",
#  "1.2",
#  "1.3",
#  "1.4",
#  "1.5",
#  "1.6",
#  "1.7",
#  "1.8",
#  "1.9",
#  "2.0",
#  "2.1",
#  "2.2",
#  "2.3",
#  "2.4",
#  "2.5",
#  "2.6",
#  "2.7",
#  "2.8",
#  "2.9",
#  "3.0",
#  "3.1",
#  "3.2",
#  "3.3",
#  "3.4",
#  "3.5",
#  "3.6",
#  "3.7",
#  "3.8",
#  ...
#  "99.9"]
```

Oh, well... I guess this wasn't meant to be. By the way, did I mention that successive strings get extra weird if you're dealing with text that's not in English:

``` ruby
# Examples in Bulgarian with Cyrilic letters below

# Looking good!
'1999б'.succ
# => "1999в"

# WAT!!!
'1999я'.succ
# => "1999ѐ"
```

I guess not everyone will understand those examples, but let's say that most people would expect a different letter to appear after "я", which is normally the last letter in the alphabets of some Slavic languages. Of course, this has less to do with Ruby and more to do with Unicode and the character ordering there.

Generating successive strings has its uses (mostly in code golfing), but it's probably not something you'll see often in Ruby applications. If you know any cool and useful applications - please share those in the comments!

That's all I have for you today. Keep Ruby weird!

[^1]: `succ` stands for "successor".
