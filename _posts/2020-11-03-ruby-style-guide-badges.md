---
layout: post
title: Ruby Style Guide Badges
date: 2020-11-03 10:57 +0200
tags:
- Ruby
---

It's not always clear what kind of style guide a certain Ruby project
is adhering to (if any). Often contributors to the project will have
to do some digging in the code to figure this out on their own.

It'd be nice if more projects were more explicit about their code
style preference, as that's not a lot of work. It can be as simple as
adding a badge to your `README` file that looks something like:

[![Ruby Style Guide](https://img.shields.io/badge/code_style-community-brightgreen.svg)](https://rubystyle.guide)

That badge targets the community Ruby style guide, which is the basis for RuboCop's defaults, but you can
use a similar badge for other Ruby style guides as well - e.g. those from GitHub and AirBnB.

If you have your own custom style guide, but you're still using RuboCop to enforce it, you can
go with something like this generic badge and link to your RuboCop config file:

[![Ruby Style Guide](https://img.shields.io/badge/code_style-rubocop-brightgreen.svg)](https://github.com/rubocop-hq/rubocop)

Pretty simple and straightforward, right? The Markdown code for those badges is here:

``` markdown
[![Ruby Style Guide](https://img.shields.io/badge/code_style-community-brightgreen.svg)](https://rubystyle.guide)

[![Ruby Style Guide](https://img.shields.io/badge/code_style-rubocop-brightgreen.svg)](https://github.com/rubocop-hq/rubocop)
```

Feel free to tweak the badges to your liking. I hope that down the road I'll get to see more of them in (public) Ruby projects.

That's all I have for you today. Keep hacking!
