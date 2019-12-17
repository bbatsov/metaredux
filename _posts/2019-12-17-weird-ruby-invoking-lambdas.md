---
layout: post
title: 'Weird Ruby: Invoking Lambdas'
date: 2019-12-17 11:22 +0100
category: posts
tags:
- Ruby
- Weird Ruby
- Series
- Meta Advent 2019
---

*This article is part of the ["Meta Advent 2019"]({% post_url 2019-12-01-meta-advent-2019 %}) series. I've committed to writing
a new blog post here every day until Christmas.*

It's finally time for another "Weird Ruby" installment after a long
hiatus! Today's topic will be invoking (calling) procs/lambdas and it's fairly short.
For the sake of brevity I'll be using only the term `proc` going forward, but
everything in this article applies to both procs and lambdas.

How would you invoke a proc object like this one?

``` ruby
add_five = ->(x) { x + 5}
```

Usually you'd do this by using the `call` method.

``` ruby
add_five.call(10) # => 15
```

Simple enough, right? Ruby, however, has always been known for providing
numerous ways to do the same thing and for a tendency to use a lot of operator
overloading. That's why it shouldn't be a surprise that in practice you might
find more people doing proc invocation like this:

``` ruby
add_five.(10) # => 15
```

This syntax is rather elegant, but it certainly looks a bit weird, as it
deviates from the common practice to have some method name after the method
invocation operator `.` - e.g. `object.method_name(arg1, arg2)`. Still, that's not weird enough to justify an entire article.
Well, it's good that we can do even better, as there's a third way to invoke a proc:

``` ruby
add_five[10] # => 15
```

This looks a lot like some element access in a data structure, but it's actually
a proc invocation. The reason for this is that procs override the `[]`
operator. That particular usage is a remnant of the older days of Ruby and you'd
do well to avoid this syntax, as the resulting code is quite confusing. Using
`call` or `.()` will certainly result in clearer code.  Generally I think it's a
very bad idea to override `[]` for anything that doesn't map somehow to
accessing an element in an underlying data structure.

That's all I have for you today! Hopefully it was both weird and useful! See you tomorrow!
