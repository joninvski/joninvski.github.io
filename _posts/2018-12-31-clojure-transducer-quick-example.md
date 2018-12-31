---
layout: post
title: Clojure transducers quick example
comments: True
type: "blog"
short_summary: "Small example of clojure transducers"
keywords: ["clojure"]

---

By default we use `map`, `filter` and `reduce` to interact with sequences.

```clojure
(range 1 10)
;; (1 2 3 4 5 6 7 8 9)

(map inc (range 1 10))
;; (2 3 4 5 6 7 8 9 10)

(map inc (filter odd? (range 1 10)))
;; (2 4 6 8 10)
```

In this case we are using an intermediate list to filter the odd?. Also it is difficult to compose this behaviour of first filtering and the incrementing.

But we can use transducers:

```clojure
;; Create an inc transducer
(def t1 (map inc))

;; Create a filter odd transducer
(def t2 (filter odd?))
 
;;"Apply" transducers (please compare both results)
(transduce (comp t1 t2) conj (range 1 10))
(transduce (comp t2 t1) conj (range 1 10))
 
;;But it isn't necessary to add them to a list
(transduce (comp t1 t2) str (range 1 10))
```
