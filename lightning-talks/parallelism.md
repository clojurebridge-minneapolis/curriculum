Lightning Talk: Parallelism
===========================

* Tons of cores, & more in the future
* Parallelism is the present, and even more so the future
* We're not good at writing parallel & distributed code (correctly)
* Functional languages like Clojure make it easier to write correct parallel programs

> One approach, usually taken by operating system designers, assumes that the programmer is an all-knowing genius who makes no mistakes. The other approach, usually taken by database designers, assumes that the programmer is a mere mortal, so it provides strong automatic support for coordination correctness, but at some cost in flexibility. – Saltzer and Kaashoek, _Principles of Computer System Design_

Most programming languages take the "genius" approach; Clojure provides some cool alternatives that are more usuable by us mortals.

Imagine we want to count how many page loads there are to the amazing web app that we're building.

We might count visits with something like `visits := visits + 1`. Under the covers this typically works as three separate steps:
* Read the value of `visits`
* Add 1 to that value
* Write the new value back to `visits`

What if two services (Alice and Bob both) want to increment at (essentially) the same time?
* They both read `visits` and get the same value, say 100.
* They both add one to that value, yielding 101.
* One of them, say Alice, will perform their write a hair before the other, changing the value to 101.
* Bob then writes his value, which is also 101.

We've lost an increment – we wanted 102 but got 101. This is an example of a _race condition_, where the order (timing) of events causes errors.

Wikipedia's Main page was loaded 414,736,629 times in March, 2014. That's 155 times a second on average (so probably much higher on occassion). Thus these kinds of race conditions are _super_ likely.

If we instead use `swap!` in Clojure we're safe, as `swap!` ensures that processes don't step on each other's toes in nasty ways.

```Clojure
(def visits (atom 0))

(defn increment-visits [counter] (swap! counter inc))

(doall (pmap increment-visits (repeat 1000 visits)))
```


### Back

Back to the [Curriculum](../README.md)
