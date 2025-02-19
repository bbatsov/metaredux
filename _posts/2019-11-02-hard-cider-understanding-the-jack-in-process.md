---
layout: post
title: 'Hard CIDER: Understanding the Jack-in Process'
categories: posts
tags:
- Clojure
- Emacs
- CIDER
- Series
- Hard CIDER
---

There are many mysteries in life, but few are as perplexing as the
eternal question "What exactly happens during `cider-jack-in`?". Today it's time
to unravel this mystery!

## The Basics

At its heart `cider-jack-in` does something super simple - it just executes a shell
command (e.g. `lein repl`) that starts an nREPL server, waits for this server to come
to life, and then connects CIDER to it. It's really as simple as this. If I had to explain it in
more details I'd probably do it like:

* CIDER shells out and runs a command like `lein repl :headless`.
* CIDER waits for the nREPL server to start.
CIDER figures out this by parsing the output from the command and waiting for a line like
`nREPL server started on port 53005 on host localhost - nrepl://localhost:53005` to appear there.
* CIDER extracts the port of the nREPL from the preceding message.
* It connects to the running nREPL server.

Basically it's a combination of auto-starting an nREPL server and doing
`cider-connect` to it afterwards. [^1] Or is it? If it were so simple why does
the shell command executed by `cider-jack-in` for Leiningen project look like:

    [nREPL] Starting server via /usr/local/bin/lein update-in :dependencies conj \[nrepl\ \"0.6.0\"\] -- update-in :plugins conj \[cider/cider-nrepl\ \"0.23.0-SNAPSHOT\"\] -- repl :headless :host localhost

It's time to explain another confusing concept - namely auto-injection of Clojure dependencies.

## Auto-Injecting Dependencies

While CIDER's core functionality requires nothing more than an nREPL server,
there are many advanced features[^2] that depend on the presence of additional nREPL
middleware (e.g. `cider-nrepl`). In the early versions of CIDER
(up to CIDER 0.11) users had to add those dependencies themselves, which was a
painful and error-prone process.  Fortunately today that's handled
auto-magically when you're using `cider-jack-in`.

If your project uses `lein`, `boot` or `tools.deps` (`deps.edn`), CIDER will
automatically inject all the necessary nREPL dependencies (e.g. `cider-nrepl` or
`piggieback`) when it starts the server.  The injection process is extremely
simple - CIDER simply passes the extra dependencies and nREPL configuration to
your build tool in the command in runs to start the nREPL server. Here's how
this looks for `tools.deps`:

```console
$ clojure -Sdeps '{:deps {nrepl {:mvn/version "0.6.0"} cider/cider-nrepl {:mvn/version "0.22.4"}}}' -m nrepl.cmdline --middleware '["cider.nrepl/cider-middleware"]'
```

Here's a tip for you - if you don't want `cider-jack-in` to inject dependencies automatically, set
`cider-inject-dependencies-at-jack-in` to `nil`. Note that you'll have to setup
the dependencies yourself (e.g. by adding them to your project)
just as in CIDER 0.10 and older. There are few reason why you'd want to do something like this,
but the option exists.

Normally `cider-jack-in` would inject only `cider-nrepl` and `cider-jack-in-cljs` would
add `piggieback` as well. The injection, however, mechanism is configurable and
you can easily add more libraries there. Some CIDER extensions (e.g. `sayid` and `clj-refactor`) would use
this mechanism to auto-inject their own dependencies.

CIDER would also inject the most recent version of nREPL that it supports. This is a simple
trick to override the version of nREPL bundled with your build tool, so you can gain
access to the newest nREPL features. Generally that's one aspect of CIDER's inner workings
that end-users will rarely have to think about.

CIDER can also inject a Clojure dependency into your project, which is useful,
for example, if your project defaults to an older version of Clojure than that
supported by the CIDER middleware. Set `cider-jack-in-auto-inject-clojure`
appropriately to enable this.

There are other nuances of the auto-injection - e.g. for Leiningen you
can inject both dependencies and plugins, but that's outside the scope of this article.

Now it's time for a little quiz! Can you guess why CIDER does not currently
support dependency auto-injection for Gradle projects? Yeah, you're totally
right! Unfortunately there's no way to pass extra dependencies to Gradle via its
command-line interface.

## Jacking-in without a Project

If you try to run `cider-jack-in` outside a project
directory, CIDER will warn you and ask you to confirm whether you
really want to do this; more often than not, this is an accident.  If
you decide to proceed, CIDER will invoke the command configured in
`cider-jack-in-default`. Prior to CIDER 0.17, this defaulted to `lein`
but was subsequently switched to `clj`, Clojure's basic startup command.

Here's another useless tip - you can set `cider-allow-jack-in-without-project` to
`t` if you'd like to disable the warning displayed when jacking-in outside a
project. I never run CIDER outside of projects, but you might have a different style
and, as you probably noticed by now, CIDER is infinitely configurable.

## Customizing the Jack-in Command Behaviour

We now made it to my favourite part of our exploration of CIDER's jack-in - how can we
tweak the shell command it runs? There are many reasons to want to do this - most
often you'd like to include some extra profiles (e.g. the famous `fig` profile if you
are using `tools.deps`), but I can imagine some people would have to do more
creative tweaks as well.

Let's start with the simplest possible option, that's idea for one-off customizations.
You can use `C-u M-x cider-jack-in` (`C-u C-c C-x j j`) to
specify the exact command that `cider-jack-in` would run.
This option is very useful is you want to specify a something like a `lein`
or `deps.edn` profile.

Alternatively you can `C-u C-u M-x cider-jack-in`, which is a
variation of the previous command. This command will first prompt you for the
project you want to launch `cider-jack-in` in, which is pretty handy if you're
in some other directory currently. This option is also useful if your project
contains some combination of `project.clj`, `build.boot` and `deps.edn` and you want
to launch a REPL for one or the other.

Keep in mind that the examples here use only `cider-jack-in`, but this behaviour is consistent
for all `cider-jack-in-*` commands.

You can further customize the command line CIDER uses for `cider-jack-in` by
modifying the following string options:

* `cider-lein-global-options`, `cider-boot-global-options`,
`cider-clojure-cli-global-options`, `cider-gradle-global-options`:
these are passed to the command directly, in first position
(e.g., `-o` to `lein` enables offline mode).
* `cider-lein-parameters`, `cider-boot-parameters`,
`cider-clojure-cli-parameters`, `cider-gradle-parameters`: these are
usually task names and their parameters (e.g., `dev` for launching
boot's dev task instead of the standard `repl -s wait`).

Don't forget that those variables can be configured [on a per-project basis]({% post_url 2019-10-05-hard-cider-project-specific-configuration %}).

## A Note about `tools.deps` and Windows

You've probably figured out by now that using `tools.deps` with Windows
can be a bit painful. Fortunately CIDER can help with that.

To use `cider-jack-in` with `tools.deps` on Windows set the
`cider-clojure-cli-command` to `"powershell"`. This happens by default
if you are on Windows and no `clojure` executable is found. Using
`"powershell"` will Base64 encode the `clojure` launch command before
passing it to PowerShell and avoids shell-escaping issues.

You might be wondering why CIDER uses `clojure` instead of `clj`, right?  It's
pretty simple actually - `clj` simply wraps `clojure` with some
terminal-friendly functionality (`rlwrap`), that explodes if you're not in an
actual terminal. Another great mystery has been resolved!

## Epilogue

Phew! That's a lot of information! I hope now you've got a much better idea how
the jack-in process is working and that this will help you use CIDER more
effectively down the road.

There's a lot more that can be said about jack-in, but I do feel we've covered everything
essential. I'm also a bit lazy and I don't feel like writing anymore. :-)

That's all I have for you today. This episode was brought to you by [Clojurists Together](https://www.clojuriststogether.org/).
They are awesome and so are all of you! Keep hacking!

[^1]: With a pretty confusing Neuromancer/Matrix-inspired name.
[^2]: "Advanced" is a bit of stretch here - there's no ClojureScript support without Piggieback.
