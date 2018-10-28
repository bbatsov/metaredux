---
layout: post
title: "A Better Way to Compare Versions in Ruby"
date: 2018-10-28 10:53:36 +0200
categories: posts
tags:
- Ruby
---

Very often we need to compare version strings in the Ruby code we
write - e.g.  we need to handle something differently if some library
is newer than some version (perhaps because it changed its API). As
versions are often represented as strings many Rubyists would write
code like this:

``` ruby
if version > '2.3.4'
  # ...
else
  # ...
end
```

That works fine for simple use-cases, but quickly becomes problematic
if `version` is something like `2.10`, as due to the lexicographical
nature of the string comparison the string `2.10` is smaller than
`2.3` (1 is smaller than 3).

What are the more robust options then? Obviously you can roll out some
custom `Version` class with attributes like `major`, `minor`, `patch`,
`qualifier`, etc and some custom logic for the comparison and that's
probably what many people do when dealing with this problem. Recently,
however, I came upon a simpler solution.  Turns out that Ruby
has been shipping for a while the class
[`Gem::Version`](http://ruby-doc.org/stdlib-2.5.0/libdoc/rubygems/rdoc/Gem/Version.html)
and it does exactly what we need to solve.

Here's the class in action[^1]:

``` ruby
if Gem::Version.new(Psych::VERSION) >= Gem::Version.new('3.1.0.pre1')
  ::YAML.safe_load(input, whitelist_classes: [::Symbol])
else
  ::YAML.safe_load(input, [::Symbol])
end
```

You simply need to wrap the version string into an instance of `Gem::Version` and
you get the right comparison behaviour. Note that this class implements some
advanced comparison semantics and can compare properly pretty much every reasonable
version scheme in existence.

The class contains some other cool functionality. Let's play with for a bit!

``` ruby
v = Gem::Version.new('1.2.3.pre1')
# => Gem::Version.new("1.2.3.pre1")

# Check if we're dealing with a pre-release version.
v.prerelease?
# => true

# Get the release version for a pre-release version.
v.release
# => Gem::Version.new("1.2.3")

# Get the version segments.
v.segments
# => [1, 2, 3, "pre", 1]

# Bump the version.
v.bump
# => Gem::Version.new("1.3")
```

I think this class illustrates one of the wonderful aspects of Ruby -
the standard library is full of all sort of functionality and many of
the mundane problems that we're dealing with on a daily basis are
already solve for us.

Before I wrap it up, I'd like to extend a special "Thanks!" to my
friend and fellow RuboCop Core Team member [Koichi
Ito](https://github.com/koic), who introduced me to this class
recently. No matter how long I use Ruby I still keep learning new
stuff![^2]

That's all from me, folks! Keep hacking!

[^1]: That code was taken from [here](https://github.com/rubygems/rubygems/pull/2439/files).
[^2]: Or I just started to forget the things I knew at an alarming pace. :D
