---
layout: post
title: Tutorial on clojure agents and channels (bonus benchmark at the end)
comments: True
type: "blog"
short_summary: "Tutorial on how to use clojure agents and channels with a small benchmark at the end"
keywords: ["clojure"]

---

What are clojure agents and channels? To answer this question let setup a system with three different participants: *Requesters*, *boxes* and *workers*. The *boxes* will be the entity that holds a state.

```clojure
(def a-box {:items 1 :value 3})
(def b-box {:items 4 :value 1})

(def all-boxes [a-box b-box])
```

Our *requesters* need to check every box, do an operation over its state and the result of this operation tells them if they are interested in the box or not:

```
(defn interested? [box]
  (let [min-value 5
        total-value (* (:items box) (:value box))]
    (< total-value min-value)))

(def a-requester (fn [] (map interested? all-boxes)))
```

But now comes the interesting part, our *workers* can at any time change the content of the box.

```
(def a-worker
  (fn []
    (while true
      (let [box (rand-nth all-boxes)]
        (change-box-content! box)
        (Thread/sleep 1)))))
```

And the *requesters* **are not** interested in having a consistent view of the *all-boxes* collection. They are **only** interested in a consistent view on the state of each *box*. In other words, while a *requester* looks at *box-a* this box cannot change, but *box-b* can change at will. Furthermore, when the *requester* moves to look at *box-b*, he should see any changes that occurred.

Small note: the `change-box-content!` in the example should really change the *box* state. This is not supported in the way we did it as we used an immutable collection for a *box* (a clojure [hash-map](https://clojuredocs.org/clojure.core/hash-map). But all code until now was just to explain the context. It is not the final solution.

So how can we approach this problem? Clojure provides [Agents](https://clojure.org/reference/agents) that allow shared access to a mutable state in an asynchronous way.

The idea is to make each *box* an agent so it can then be read or written by *requesters* or *workers* in a safe manner. As agents are asynchronous, they will allow the *requesters* to start looking at other boxes even if a previous box calculation is "stuck". This can happen if a *worker* is currently changing it.

Our boxes will now be described as agents:

```
(require 'clojure.core.async) ;; you need to require clojure.async

(def box-a (agent {:items 1 :value 1)})
```

And our requesters need to [dereference](https://clojure.github.io/clojure/clojure.core-api.html#clojure.core/deref) the agent to look at the box value. This can be done with the `deref` function or with the `@` alias.

```
@box-a
;; is equal to
(deref a)
```

Our requester now looks like (note `@` to fetch the context of a box before passing it to the `interested?`):

```
(def a-requester (fn [] (map #(interested? @%) all-boxes)))
```

And our *worker* should now use the [send](https://clojure.github.io/clojure/clojure.core-api.html#clojure.core/send) function.


```
(defn work [box]
  (Thread/sleep 100) ;; lets pretend putting a item in the box is slow
  (update box :items inc))

(def a-worker
  (fn []
    (while true
      (let [box (rand-nth all-boxes)]
        (send box work)
        (Thread/sleep 200)))))
```

Now the *workers* can happily change boxes knowing that a *requester* will never see a half "worked" box. But there is still a problem. **Requesters** are still looking at each *box* in sequence. If one box is "busy" with a worker changing it, the requester needs to wait for it. We can solve this by making requesters also use the `send` method to the agent.

```
;; This won't work
(def a-requester
  (fn [] (map #(send @% interested?) all-boxes)))
```

But the problem is that `send` is asynchronous. You don't know the result of the operation. The result of the operation will be the new box state, which is also not what you want. The `interested?` function returns a boolean, not a box state. So what we need is to get some way to return the result. Here come clojure [channel](https://clojuredocs.org/clojure.core.async/chan)'s to the rescue.

Every time a *requester* needs to look at all the boxes in the list it will first create a channel and pass that channel to each function that sees if the box is interesting. The result is passed back via that channel. Once the requester receives all responses he can go on with his life.

```
(def a-requester
   (fn []
     (letfn [(interested-aux? [box channel]
                (go (>! channel (interested? box)))
                box)
              (wait-for-responses [channel n-msgs-to-wait]
                (loop [responses-received 0]
                   (if (< responses-reiceived n-msgs-to-wait)
                    (do
                      (<!! channel) ;; just waiting for the response, not doing anything with it
                      (recur (inc responses-received))) ;; wait for the next message
                    (print "Received all responses"))))]
      (let [buff-size 100
            channel (chan buff-size)
            n-boxes (count all-boxes)]
        (dorun (map #(send % interested-aux? channel) all-boxes))
        (wait-for-responses channel n-boxes)))))
```

We created the `interested-aux?` inner function for two reasons.
First the result of the function that is passed to `send` will be the new agent state. As so we could not use the `interested?` function directly so the `interested-aux?` function returns the original box at the end.
Secondly, the `interested-aux?` function sends the value of `interested?` via the `>!` macro inside a `go` block.  This is an asynchronous way of putting values into channels.

The `wait-for-responses` inner function does exactly what the name indicates. It waits for `n-msgs-to-wait` to be received via the channel (`<!!` is a blocking fetch) and when it receives all responses it prints a string. In this example we are ignoring the value that was sent in the channel. We just care that it was sent.

Putting it all together, and including a benchmark at the end just for fun:

```
(ns main
  (:require [clojure.core.async :refer (go <!! >! chan)]
            [criterium.core :refer (bench with-progress-reporting)])
  (:gen-class))

(defn create-box [id]
  (agent {:id id :items 1 :value 1}))

(def all-boxes (doall (map create-box (range 0 1000))))

(defn work [box]
  (Thread/sleep 100) ;; lets pretend putting a item in the box is slow
  (update box :items inc))

(defn create-worker []
 (while true
  (let [box (rand-nth all-boxes)]
   (send box work)
   (Thread/sleep 200))))

(defn interested? [box]
  (let [min-value 5
        total-value (* (:items box) (:value box))]
    (< total-value min-value)))

(defn create-requester []
   (letfn [(interested-aux? [box channel]
              (go (>! channel (interested? box)))
              box)
            (wait-for-responses [channel n-msgs-to-wait]
              (loop [responses-received 0]
                 (if (< responses-received n-msgs-to-wait)
                  (do

                    (<!! channel) ;; just waiting for the response, not doing anything with it
                    (recur (inc responses-received))) ;; wait for the next message
                  (print "Received all responses"))))]
    (let [buff-size 100
          channel (chan buff-size)
          n-boxes (count all-boxes)]
      (dorun (map #(send % interested-aux? channel) all-boxes))
      (wait-for-responses channel n-boxes))))

(dotimes [_ 10] (go (create-worker)))

(with-progress-reporting (bench (time (create-requester))))
```
