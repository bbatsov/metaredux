---
layout: post
title: 'nREPL 0.7: Introducing a Native EDN Transport'
date: 2019-06-21 10:17 +0300
categories: posts
tags:
- Clojure
- nREPL
---

Today marks the release of the first milestone from the nREPL 0.7 series - namely nREPL 0.7.0-alpha1.
The highlight of the release is the addition of a native (built-in) EDN transport to complement
the default bencode transport that nREPL has shipped since day 1.

## Understanding the EDN Transport

The main difference between the bencode transport and the EDN one is that instead of dealing with bencode dictionaries and lists, and being limited to integer and byte strings, you'd be sending and receiving EDN data, including maps, vectors, lists, sets, strings, keywords and symbols. The structure of the messages is pretty much the same, only the data format changes.
This may be useful in a couple of usecases:

* In some clients, including ClojureScript ones, where EDN is easier to handle than bencode (as it's supported out of the box).
* This transport exposes richer data types and potentially we can have middleware return different data when using it.

You should keep in mind, however, that it's not clear whether it's a good idea to return different data based on the data format, so for now we've opted to return
exactly the same data in bencode and EDN responses. Currently we're mostly aiming to address the first point - making it easier for some clients to speak with nREPL.
In this sense the project is pretty similar to [Fastlane](https://github.com/nrepl/fastlane) which brought JSON and MessagePack transports to nREPL.
We remain committed to bencode and we believe that it's the optimal default encoding format due to its simplicity and universal nature, but we also acknowledge that EDN might be more
convenient in a Java or JavaScript environment.

The introduction of the EDN transport does require more specification of the message format, as bencode's simplicity left some room for interpretation here and there (e.g. for bencode strings, symbols and keywords are basically the same thing).
Some of the things that we had to specify for the EDN requests are that `:op` values are strings, and `:status` is either a keyword, or a set of keywords.
This may cause some surprising, unintended consequences, thus tweaks to the EDN message format may be possible until the transport is declared mature.
Documentation of the message format using `clojure.spec` is planned to help address any confusion. Now let's take a close look at a practical examples of
those differences. Consider the following four `eval` requests:

``` clojure
{:op "eval"  ...}
{:op :eval ...}
{"op" "eval" ...}
{"op" :eval ...}
```

The first one is considered canonical, though nREPL will accept the second one as well. The third and fourth one will not work, as ops have to be keywords if you're using the EDN transport.
Note that the change doesn't affect messages encoded with bencode, but mostly direct usage of the nREPL client API. That's why we assume that the impact from it would be small if any.

## Using the EDN Transport

I guess by now you're wondering how can you play with the new transport yourselves.
Using the EDN transport is pretty simple. You just need to start an nREPL server with EDN transport and you're good to go:

``` clojure
(require
 '[nrepl.server :as server]
 '[nrepl.transport :as transport])

(server/start-server :port 12345 :transport-fn transport/edn)
```

You can also start an nREPL with a EDN transport using `clj`[^1]:

```
$ clj -Sdeps '{:deps {nrepl {:mvn/version "0.7.0-alpha1"}}}' -m nrepl.cmdline -t nrepl.transport/edn
nREPL server started on port 63266 on host localhost - nrepl+edn://localhost:63266
```

Right now there are no 3rd party clients that support the EDN transport, but you can also use nREPL built-in
client to test it:

```
$ clj -Sdeps '{:deps {nrepl {:mvn/version "0.7.0-alpha1"}}}' -m nrepl.cmdline -t nrepl.transport/edn --connect --host localhost --port 63266
```

Alternatively you can just play with the client in the REPL:

``` clojure
(require '[nrepl.core :as nrepl])

(with-open [conn (nrepl/url-connect "nrepl+edn://localhost:63266")]
  (-> (nrepl/client conn 1000)    ; message receive timeout required
      (nrepl/message {:op "eval" :code "(+ 1 2 3)"})
      nrepl/response-values))

;; => 6
```

As you can see `nrepl/url-connect` is smart enough to figure out which transport to use based on the URI scheme (in this case "nrepl+edn").

There's nothing magical about the EDN transport and using it is pretty much transparent at this point.

## Closing Thoughts

That's the first iteration of the EDN transport and it's probably rough around the edge here and there. Getting some testing and feedback would be awesome!
I hope this post will inspire at least a few of you to play with the new transport and find some good use for it!

I'll also use this post as an opportunity to invite all of you to check out the [open tickets](https://github.com/nrepl/nrepl/issues) on nREPL's issue tracker and try your hand at tackling some of them. It's lots of fun!

[^1]: You can also specify the nREPL transport to use with Leiningen or via `nrepl.edn`. I chose `clj` for this example for simplicity's sake.
