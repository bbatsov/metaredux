---
layout: post
title: 'Weird Ruby: Heredoc Delimiters'
date: 2022-01-20 08:49 +0200
category: posts
tags:
- Ruby
- Weird Ruby
- Series
---

Remember how I thought that the [syntax for single-quoted heredocs was weird]({% post_url 2022-01-20-weird-ruby-heredoc-delimiters %})? I've got something even weirder for you today!

Turns out that when using single-quoted heredoc delimiters you can have pretty much anything in them, including spaces!

``` ruby
<<'END HTML'
  <html>
    ...
  </html>
END HTML

<<'\n!"£$%^&*()-=_+[];#{}:@~,./<>?|`\'
hello
\n!"£$%^&*()-=_+[];#{}:@~,./<>?|`\
```

It never crossed my mind that something like this would be possible until I came across this [RuboCop ticket](https://github.com/rubocop/rubocop/issues/10361), asking for spaces in delimiters to be allowed. As most editors are unlikely to handle this properly, and it looks pretty... weird, I think that you should restrain yourselves from using such heredoc delimiters.

That's all I have for you today. Keep Ruby Weird!
