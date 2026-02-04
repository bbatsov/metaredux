---
layout: post
title: Supercharge Your Bash History
date: 2020-07-07 12:41 +0300
tags:
- Unix
- Bash
- Productivity
---

For some weird reason I've decided to abandon my insane ZShell
setup for a while and switch to a vanilla Bash. Guess I needed a bit of simplicity in my life.
One thing about my initial Bash experience that drove me nuts is the way it handles the
history out-of-the-box:

* Each shell session has its own history and once you terminate a session its history
is dumped to `.bash_history`, overwriting whatever was there in the past. For me this pretty much kills the value of
persistent shell history. Note that if you
don't exit cleanly your history will just be lost.
* As a corollary - you can't really share history between different shell sessions. Each shell loads the history file on startup
and augments it whatever commands were executed in it, after loading the file. If you ran a build command in shell A you can't
easily recall it in shell B.
* It keeps duplicated entries in the history.
* By default it doesn't store many history entries (only the last 500 commands).

Obviously there's no right way to do shell history and what you consider right or optimal depends on how exactly are using your shell.
I know many people who hate sharing data between shell sessions, as they want to keep them isolated for various reason.

On the other hand - I don't know a single person who likes their shell history to be constantly overwritten.
Let's teach Bash to append to the history file instead
of overwriting it! Just add the following to your `.bashrc`:

``` shell
# append to the history file, don't overwrite it
shopt -s histappend
```

Note that some Linux distros (e.g. Ubuntu) might be enabling this shell option
in the default `.bashrc` they copy to each user's home folder.

Now, let's increase the history size and teach Bash to ignore duplicate entries in the history:

``` shell
# don't put duplicate lines or lines starting with space in the history.
# See bash(1) for more options
HISTCONTROL=ignoreboth

# for setting history length see HISTSIZE and HISTFILESIZE in bash(1)
HISTSIZE=100000
HISTFILESIZE=10000000
```

You can obviously go really big here, but unless you have a very fast SSD I would not recommend
it, as reading the history can add a bit of latency to new shell sessions.

Now, we're moving to the crux of it - let's teach Bash to update the history after each
command we invoke and to reload it. The reloading is what ensures that different shell
sessions are synced in terms of history.

``` shell
# append and reload the history after each command
PROMPT_COMMAND="history -a; history -n"
```

`history -a` writes to the history file and `history -n` reloads the history from the file, but with a twist - it loads only the new entries that were added there. This makes it way more efficient
than another similar (and quite popular) approach - namely using `history -a; history -c; history -r`. Full
reloads of a huge history file would exhibit themselves as slight delays after each command you run. The solution I've
suggested should largely avoid them.

You might also want to remove the use of certain commands from your history, whether for privacy or readability reasons. This can be done with the `$HISTIGNORE` variable. It’s common to use this to exclude `ls` (and similar) calls, job control built-ins like `bg` and `fg`, and calls to `history` itself:

``` shell
HISTIGNORE='ls:ll:cd:pwd:bg:fg:history'
```

Feel free to add here any other commands that you don't want to store in the history.
Note that here you're specifying exact matches for exclusion - the above config would
exclude `ls`, but it won't exclude `ls projects`. Most of the time you'd be using this
with commands invoked without arguments.

So, putting it all together, that's my magic recipe to supercharge Bash's history:

``` shell
# place this in your .bashrc

# don't put duplicate lines or lines starting with space in the history.
# See bash(1) for more options
HISTCONTROL=ignoreboth

# append to the history file, don't overwrite it
shopt -s histappend
# append and reload the history after each command
PROMPT_COMMAND="history -a; history -n"

# ignore certain commands from the history
HISTIGNORE="ls:ll:cd:pwd:bg:fg:history"

# for setting history length see HISTSIZE and HISTFILESIZE in bash(1)
HISTSIZE=100000
HISTFILESIZE=10000000
```

That's all I have for you today! Keep hacking!
