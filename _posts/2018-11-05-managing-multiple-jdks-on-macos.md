---
layout: post
title: Managing Multiple JDKs on macOS
categories: posts
tags:
- Clojure
- Java
- Tutorial
---

## Prologue

Installing JDKs wasn't something I needed to do often in the past -
after all, Java releases were usually 3-4 years apart. Recently, however,
this has changed[^1] and I find myself installing a new JDK every 6
months. There's also the problem that now I have to switch between
more JDKs to test things...

I did a bit of research into streamlining my workflow with multiple
JDKs and I thought I might share some of my findings with you.
My workflow is macOS-specific, but I think that users of other Unix-like
operating system can easily adapt it to their environment.

Another thing to note about my workflow is that it's very lightweight
and doesn't depend on the usage of any third-party tools. You won't
find here anything about using [SDKMAN!](https://sdkman.io) and
[jEnv](http://www.jenv.be). You can think of this post as something in
the spirit of the awesome talk [Programming with Hand
Tools](https://www.youtube.com/watch?v=ShEez0JkOFw) - sometimes
the journey is way more important than the destination itself.

<!--more-->

## Installation

I wasn't always a macOS user. Actually during most of my career I was
actually a GNU/Linux user.[^2] While I really like macOS and I find it
to be the most balanced (in terms of development and desktop
experience) OS out there, my transition from Linux wasn't
painless. Especially when it came to setting up my preferred
programming environment...

When I was a GNU/Linux user, I always installed Java (and pretty much
everything else) via my distro's package manager (e.g. `dnf`) . Most
distros had in their repositories the last 2-3 JDK releases and this
was everything I needed. What's more - Linux distros typically
provided some mechanism to switch between the JDKs you had installed
(e.g. Fedora has the `alternatives` program).

When I transitioned to macOS I really missed a proper package manager
and such version management utilities. It felt so weird to have to
download and install libraries and applications manually. Sure,
there's `homebrew`, but it's not quite like `dnf`, `apt`, `pacman`,
and friends. It's getting better each year, though. However, I never
installed Java through `brew` for some reason.  Maybe I just lost my
console edge or something like this, but I'd simply go to Oracle's
website, download the latest image, double-click it and be done with
it. Things just worked.

Now, however, the OracleJDK's terms of use have changed (you can only
use it for development purposes without paying for it), and I decided
I'd better switch my development environment to OpenJDK, so it'd match
whatever I'm using in production down the road.

The OpenJDK doesn't have a convenient `dmg` installation image - it's
distributed simply as a tarball. No problem! Installing this tarball
is trivial:

``` console
$ tar xf tar xf openjdk-11.0.1_osx-x64_bin.tar.gz
$ sudo mv jdk-11.0.1.jdk /Library/Java/JavaVirtualMachines
```

You just extract it and move it where macOS expects JDKs to be
installed. You can check that now macOS picks up the right version of
Java:

``` console
$ java -version
openjdk version "11.0.1" 2018-10-16
OpenJDK Runtime Environment 18.9 (build 11.0.1+13)
OpenJDK 64-Bit Server VM 18.9 (build 11.0.1+13, mixed mode)
```

## Behind the Curtains

Have you ever wondered what happens to your old JDK once you've
installed a new one? Well, they just stick around in
`/Library/Java/JavaVirtualMachines`. How convenient! You can easily
check what JDKs are available there either by doing a simple `ls` or by
using the `java_home` wrapper command:

``` console
$ ls /Library/Java/JavaVirtualMachines
jdk-10.0.1.jdk   jdk-11.0.1.jdk   jdk1.8.0_131.jdk

$ /usr/libexec/java_home -V
Matching Java Virtual Machines (3):
    11.0.1, x86_64:	"OpenJDK 11.0.1"	/Library/Java/JavaVirtualMachines/jdk-11.0.1.jdk/Contents/Home
    10.0.1, x86_64:	"Java SE 10.0.1"	/Library/Java/JavaVirtualMachines/jdk-10.0.1.jdk/Contents/Home
    1.8.0_131, x86_64:	"Java SE 8"	/Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents/Home

/Library/Java/JavaVirtualMachines/jdk-11.0.1.jdk/Contents/Home
```

When you've got multiple JDKs, by default the macOS Java wrapper
commands (e.g. `java`, `javac`, etc) would just use whatever's the
newest JDK, but you can also easily switch between the installed JDKs.

## Switching between JDKs

The simplest way to switch between JDKs is to set `JAVA_HOME` to whatever JDK you want to use:

``` console
# switch to Java 8
$ export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents/Home
$ java -version
java version "1.8.0_131"
Java(TM) SE Runtime Environment (build 1.8.0_131-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.131-b11, mixed mode)
# go back to the default
$ unset JAVA_HOME
$ java -version
openjdk version "11.0.1" 2018-10-16
OpenJDK Runtime Environment 18.9 (build 11.0.1+13)
OpenJDK 64-Bit Server VM 18.9 (build 11.0.1+13, mixed mode)
```

You don't really need to remember the exact path to each installed JDK, as `java_home` can help you with this:

``` console
$ /usr/libexec/java_home -v 1.8
/Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents/Home
# switch to Java 8
$ export JAVA_HOME=$(/usr/libexec/java_home -v1.8)
$ java -version
java version "1.8.0_131"
Java(TM) SE Runtime Environment (build 1.8.0_131-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.131-b11, mixed mode)
```

Notice the version scheme change after Java 8 - up to it JDK versions
were internally 1.x, but since JDK 9 they became 9, 10, and so on. In
practical terms this means you can't pass `-v 8` to `java_home` - you
have to pass `-v 1.8` instead.

Armed with this knowledge, you can devise some helpful shell function
like this:

``` shell
switch_java() {
    echo "Switching to Java $1"
    export JAVA_HOME=`/usr/libexec/java_home -v $1`
    java -version
}
```

Add this to your shell config (e.g. `.bashrc`), reload the config
(e.g. by using `. .bashrc`), and use it like this:

``` console
$ switch_java 1.8
Switching to Java 1.8
java version "1.8.0_131"
Java(TM) SE Runtime Environment (build 1.8.0_131-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.131-b11, mixed mode)
$ switch_java.sh 11
Switching to Java 11
openjdk version "11.0.1" 2018-10-16
OpenJDK Runtime Environment 18.9 (build 11.0.1+13)
OpenJDK 64-Bit Server VM 18.9 (build 11.0.1+13, mixed mode)
```

I'll leave it as an exercise for the readers to extend this function to
show you all available JDKs and some indication which one is the
currently active. Such modification will be fairly straightforward.

## Epilogue

That's all, folks! Hopefully you enjoyed this post and learned
something useful from it!

In the modern age software engineers reach for
third-party solutions for trivial things and I find this a bit
troubling. That's really the entire point of this post - rolling up
your sleeves and getting dirty can be fun and rewarding. You get to
learn more, you get to build a workflow that's tailored to your needs,
and you get to have some fun in the process. That makes you feel like
a real hacker, doesn't it?

Journey before destination...

[^1]: <https://mreinhold.org/blog/forward-faster>
[^2]: <http://batsov.com/articles/2012/09/09/from-linux-to-osx-1-year-later/>
