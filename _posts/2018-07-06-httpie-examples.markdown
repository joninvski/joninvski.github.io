---
layout: post
title: HTTpie examples
comments: True
type: "blog"
short_summary: "Small cheatsheet containing examples on how to use HTTPie (curl replacement)"
keywords: ["clojure"]

---

CURL is the default tool to do HTTP requests from the command line.
HTTPie tries to improve it by making it more user friendly, including a
more natural syntax and colorized output.

Install it via the instructions on [Installation Page](https://github.com/jakubroztocil/httpie#2installation).

To use it do:

```
http https://swapi.co/api
```

Only see the response headers (`h`):

```
http -p 'h' https://swapi.co/api
```


Only print the response body (`B`):

```
http -p 'B' https://swapi.co/api
```

Use different HTTP action:

```
http POST https://swapi.co/api/films/1
http PUT https://swapi.co/api/films/1
http DELETE https://swapi.co/api/films/1
```

Print the request headers:

```
http -p 'H' https://swapi.co/api/films/1
```

Define and print the request body in JSON format.
Note the `Content-Type` header.

```
http --print 'H' POST https://swapi.co/api/films/1  rating='123'
```

Define and print the request body in form encoded format.
Again note the `Content-Type` header.

```
http --print 'H' -f POST https://swapi.co/api/films/1  rating='123'
```

Pass parameter via URL string. Check the URL path in the first line:

```
http --print 'H' POST https://swapi.co/api/films/1  rating=='123'
```

And this concludes our quick cheatsheet for HTTPie.
