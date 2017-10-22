---
layout: post
title: Configure host and port when starting clojure's boot 
comments: True
type: "blog"
short_summary: "Using clojure's boot inside a docker container means you need to configure and expose the correct port for your repl server. Here's how to do it."

---

When you start [boot](https://github.com/boot-clj/boot) it is possible to set host and port number the repl server will bind to. Just pass the `-b` and `-p` parameters.

```
boot repl -b 0.0.0.0 -p 40001"
```

Passing these parameters is necessary when you want to start a repl via boot in a docker container and then do interactive development with that container.

Try it for yourself, you can use the following `docker-compose.yml` file

```
version: "2.1"

services:
  repl:
    image: clojure:boot-alpine
    working_dir: /usr/src/app
    volumes:
      - .:/usr/src/app
    environment:
      - BOOT_LOCAL_REPO=/usr/src/app/.m2
      - REPL_PORT
      - REPL_HOST
    ports:
      - "0.0.0.0:${REPL_PORT}:${REPL_PORT}"
    command: "boot repl -b ${REPL_HOST} -p ${REPL_PORT}"
```

And don't forget to set the `.env` file:

```
REPL_PORT=40001
REPL_HOST=0.0.0.0
```

Then to start you just need to run:


```
docker-compose run --service-ports repl
```

Notes:

 * `--service-ports` parameter tells docker-compose to use the specified ports.
 * `BOOT_LOCAL_REPO` env var indicates where to store the dependencies. Here we're pointing them to `/usr/src/app/.m2` that is mapped to the current host directory. This way whenever you start new `repl` containers they will not need to "refetch" the dependencies.
