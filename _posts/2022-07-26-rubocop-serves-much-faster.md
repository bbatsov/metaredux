---
layout: post
title: RuboCop Serves (Much) Faster
date: 2022-07-26 10:34 +0300
tags:
- RuboCop
- Ruby
---

RuboCop's motto has always been "The Ruby Linter that Serves and Protects".[^1] Now, with the addition of a [server mode](https://docs.rubocop.org/rubocop/usage/server.html) in RuboCop 1.31 that motto is truer than ever! But first - a bit of background.

Most people value speed and we've been on a decade-long journey in RuboCop to improve the performance:

- we've started with the [Commissioner](https://github.com/rubocop/rubocop/blob/master/lib/rubocop/cop/commissioner.rb) that triggered all cops during a single parser run for each file (originally we were doing some crazy ineffective things)
- then we've made it possible to run inspection in parallel (that's the default behavior these days)
- then we've started to [cache the results](https://docs.rubocop.org/rubocop/usage/caching.html) of inspection runs which resulted in lighting fast subsequent RuboCop runs
- we've been doing frequent surgical performance tweaks in slow cops

One problem kept alluding us, though - RuboCop's slow startup time. As RuboCop grew bigger and more complex it needed to do more work on startup:

- load more and more cops and utility (and `require`s in Ruby are kind of slow)
- process the configuration, which was getting more complex itself

As a result the main complaint on the performance front became that it was pretty unpleasant to run RuboCop within a text editor (there are numerous plugins that integrate linters with editors) - it was starting too damn slow (a few seconds in some cases) and not every editor was running linters in some async fashion.

Eventually a popular workaround emerged - [rubocop-daemon](https://github.com/fohte/rubocop-daemon). As the name of the project implies it runs RuboCop as a daemon (server) process, so the startup overhead happens just once and subsequent RuboCop invocations are much faster as a result of this. With RuboCop 1.31 we've decided to
make the lives of everyone a bit simpler and brought the same concept to RuboCop itself. Now all you have to do is run RuboCop like this:

    $ rubocop --server

If a server process has not started yet, this will start a server process (bound to the current directory) and execute inspection with the help of the server. Magic!
You can also start the server without an inspection run:

    $ rubocop --start-server
    RuboCop server starting on 127.0.0.1:55772.

If a server is already running, the command only displays the server's PID. A new server will not be started.

The `rubocop` command is executed using the server process if a server is started.
Whenever a server process is not running, it will load the RuboCop runtime files and execute normally. (same behavior as with RuboCop 1.30 and lower)

Note that RuboCop servers are associated with the directory in which they were started, so you can have several of them running:

``` shellsession
$ ps waux | grep rubocop
bozhidar 17801  0.2  0.5 1227840 87804 ?       S    10:50   0:00 rubocop --server /home/bozhidar/projects/rubocop
bozhidar 17990  0.0  0.3 117240 53172 ?        S    10:54   0:00 rubocop --server /home/bozhidar/projects/rubocop-ast
```

The `rubocop` command will connect to right server automatically, based on the folder where it was run.

You should keep in mind that you'll need to restart the server manually if you change RuboCop's configuration. You can do so like this:

    $ rubocop --restart-server

Make sure to run this command in the same folder you've started the server. Down the road we'll likely auto-restart the server on configuration changes.

So how much faster is RuboCop with the server mode? According to our ["scientific benchmarks"](https://github.com/rubocop/rubocop/pull/10706#issuecomment-1152382752) it might be as much as 900x faster. I think that's fast enough!

That's all I have for you today! Hopefully I'll have more fun performance updates to share on our road to a much faster RuboCop 2.0!

[^1]: It was meant to be a funny pun, that sadly has become somewhat controversial in recent years.
