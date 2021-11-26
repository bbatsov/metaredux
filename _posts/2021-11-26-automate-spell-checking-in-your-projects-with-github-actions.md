---
layout: post
title: Automate Spell-checking in Your Projects with GitHub Actions
date: 2021-11-26 10:51 +0200
tags:
- Meta
- Spell Checking
- GitHub Actions
---

A couple of years ago I wrote an article about [dealing with typos in your source code]({% post_url 2019-05-24-eradicate-typos-in-source-code %}). Today I'll follow up
with a simple recipe to automate this spell-checking process using GitHub Actions (GHA).[^1]

Lately I've been adding the following [GHA workflow](https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions) to all of my OSS projects:

``` yaml
name: Spell Checking

on: [pull_request]

jobs:
  codespell:
    name: Check spelling with codespell
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: [3.8]
    steps:
      - uses: actions/checkout@v2
      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v2
        with:
          python-version: ${{ matrix.python-version }}
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install codespell
          if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
      - name: Check spelling with codespell
        run: codespell --ignore-words=codespell.txt || exit 1
  misspell:
    name: Check spelling with misspell
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install
        run: wget -O - -q https://git.io/misspell | sh -s -- -b .
      - name: Misspell
        run: ./misspell -error
```

There's nothing fancy here. We're just installing a couple of popular spell-checkers for code and we run them
on everything within the Git repository. If they discover any problems the build will fail.

Just save the code above under `.github/workflows/spell_checking.yml` and you're good to go.
The workflow will get triggered for each subsequent pull request.

A couple of things to note:

- This workflow uses both [codespell](https://github.com/codespell-project/codespell) and [misspell](https://github.com/client9/misspell). Using both might be an overkill for some of you. Personally, I like `misspell` more, as it's super fast.
- It's a good idea to run both tools locally first and address any existing typos.
- You may want to limit the checks only to files that were changed in the pull request.
- You may want to specify a locale for `misspell` if you want to enforce a specific flavor of English (e.g. `misspell -locale US`).
- This should probably be made a [reusable workflow](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows). I won't be surprised if someone actually has created a public reusable workflow for spell-checking code already.

That's all I have for you today. Big thanks to my fellow OSS hacker [Koichi
Ito](https://github.com/koic), who came up with the idea of making
spell-checking a CI step. In hindsight it seems like a very obvious thing to do,
but it was always an afterthought for me. I'd be curious to hear how others are
dealing with typos in their codebases.

[^1]: The outlined approach can easily be adapted for any other CI.
