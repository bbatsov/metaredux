---
layout: post
title: RuboCop Meets Minitest
date: 2019-10-04 17:04 +0300
categories: posts
tags:
- Ruby
- RuboCop
- Minitest
---

RuboCop has had an ["official" extensions for RSpec](https://github.com/rubocop-hq/rubocop-rspec) for years.
In fact the `rubocop-rspec` project was the thing that pushed us in the direction to make [RuboCop itself modular]({% post_url 2019-05-22-a-modular-rubocop %}) and to make it easy
to create RuboCop extensions in general. There's still a lot of work remaining to be done when it comes to providing
a robust extension API, but the explosion of extensions in recent years certainly contributed to a lot of progress on that front.
Today one of the important goals of the RuboCop project is
to provide support for every important library or framework in the Ruby ecosystem, as they
typically have their own sets of best practices. The big goal probably deserves its own post. Today,
however, I just want to share with you a small milestone towards achieving it.

I'm a big fan of RSpec, but I also have a lot of appreciation and respect for the simple design of
Minitest. Today I'm happy to announce that for a while we (RuboCop's team) have been working
on [rubocop-minitest](https://github.com/rubocop-hq/rubocop-minitest), which extends RuboCop with some extra understanding of Minitest.
You can read a bit more about the project [here](https://docs.rubocop.org/projects/minitest/en/stable/).

Setting up `rubocop-minitest` is really simple. Just add it to your `.rubocop.yml` and you're good to go:

``` yaml
require:
  - rubocop-other-extension
  - rubocop-minitest
```

By default all the cops are scoped to `**/test/**/*`, which in theory should work fairly well for most
projects. Keep in mind, however, that you might have to adjust it if your project utilizes some non-standard
folder layout.

On a related note - we've also launched a [Minitest Style Guide](https://minitest.rubystyle.guide) alongside
the extension. Right now both the gem and the style guide are pretty basic, but I hope that with the help of the awesome Ruby community
we'll level them up quickly. Fortunately for us, the simplicity of Minitest means that there's not that much work
to do in general, at least compared to RSpec.

That's all from me for now. I hope you'll enjoy `rubocop-minitest` and the Minitest Style Guide. I'm looking forward
to hearing what you think about them. Until next time! Keep hacking!
