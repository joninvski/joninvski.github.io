
CURL is the default tool to do HTTP requests from the command line.
HTTPie tries to improve it by making it more user friendly, including a
more natural syntax and colorized output.

Install it via the instructions on [Installation Page](https://github.com/jakubroztocil/httpie#2installation).

```
http https://swapi.co/api
```

Only see the headers

```
http -p h https://swapi.co/api
```


Only print the body

```
http -p b https://swapi.co/api
```

Use different HTTP action

```
http POST https://swapi.co/api/films/1
http PUT https://swapi.co/api/films/1
http DELETE https://swapi.co/api/films/1
```

Print the request headers

```
http -p 'H' https://swapi.co/api/films/1
```

Print the request headers

```
http -p 'H' https://swapi.co/api/films/1
```

Define and print the request body in JSON format.
Note the `Content-Type` header.

```
http --print 'HB' POST https://swapi.co/api/films/1  rating='123'
```

Define and print the request body in form enconded format
Note the `Content-Type` header.

```
# Note the `Content-Type` header
http --print 'HB' -f POST https://swapi.co/api/films/1  rating='123'
```

Pass parameter via URL string

```
# Note the `Content-Type` header
http --print 'HB' POST https://swapi.co/api/films/1  rating=='123'
```
