---
layout: post
title: Makefile to run a clojure app in docker
comments: True
type: "blog"
short_summary: "Learn how to create a Makefile that runs a clojure app"
keywords: ["docker", "bash", "clojure"]

---

Let's say we have a small clojure app with two files:

* **build.boot**
```clojure
(set-env! :resource-paths #{"src"})
```

* **src/hello.clj**
```clojure
(ns hello)

(defn say-hello
  (println "hello"))

(defn -main
 (say-hello))
```

And we want to run this app via docker called via a Makefile.
These are the steps you need to do:

Add a `run` boot task to the **build.boot** file:

```clojure
(require '[hello])

(deftask run []
  (with-pass-thru _
    (hello/-main)))
```

Create a **Makefile** with the run target:

```make
run:
  docker run --rm -e BOOT_LOCAL_REPO=/usr/src/app/.m2 -it -w /usr/src/app -v ${PWD}:/usr/src/app clojure:boot-alpine \
     boot run
```

Now you can run simply by doing:

```
make run
```

It is normally also useful to add a REPL target to the makefile:

```make
repl:
  docker run --rm -e BOOT_LOCAL_REPO=/usr/src/app/.m2 -it -w /usr/src/app -v ${PWD}:/usr/src/app clojure:boot-alpine \
     boot repl
```
