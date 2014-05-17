Module 4: Functions
===================

* What are functions?
* Naming functions
* Important functions
  * Collection functions
* `map` and `reduce` - Functions that take other functions
* `partial` and `comp` - Functions that build new functions

## What are functions?

You have already seen some functions, such as `count`, `conj`, `first`, and `rest`. All the arithmetic we did had functions, as well: `+`, `-`, `*`, and `/`. What does it mean to be a function, though?

A _function_ is an independent, discrete piece of code that takes in some values (called _arguments_) and returns other values. Let's see an example:

```clj
(fn [subtotal]
  (* 1.085 subtotal))
```

This is a simple function in it's most basic form.  It takes one value, refered to as `subtotal`, and multiples by 1.085.  However, without any name or documentation, the purpose and use for this function is a little unclear.

```clj
(def total-bill
  (fn [subtotal]
    (* 1.085 subtotal)))
```

Here, the same function is bound to the name `total-bill`, just like we have previously done with other values.  The name makes it's purpose a little more clear.

```clj
(def total-bill
  "Given the subtotal of a bill, return the total after tax."
  (fn [subtotal]
    (* 1.085 subtotal)))
```

In addition to naming the function, we can also attach a description of it's purpose to it.  Any defined name can be documented this way, and this documentation can be retrieved interactively.

Since defining new functions is a prevalent aspect of writing Clojure programs, `defn` condenses how functions are declared.

```clj
(defn total-bill
  "Given the subtotal of a bill, return the total after tax."
  [subtotal]
  (* 1.085 subtotal))
```

In this code:

* `defn` a contraction of def and fn that defines a named function.
* `total-bill` is the name of this function.
* The string on the next line is the documentation for the function, which explains what the function does. This is optional.
* `[subtotal]` is the list of arguments. Here, we have one argument called `subtotal`.
* `(* 1.085 subtotal)` is the _body_ of the function. This is what executes when we use the function.

To use `total-bill`, we _call_ the function, just like we've done with all the functions we've already used.

```clj
(total-bill 8.90) ;=> 9.6565
(total-bill 50)   ;=> 54.25
(total-bill 50/7) ;=> 7.75
```

Functions can also take more than one argument. Let's make a `total-with-tip` function that additionally takes a tip percentage and calculates the total amount paid:

```clj
(defn total-with-tip
  [subtotal tip-pct]
  (* 1.085 subtotal (+ 1 tip-pct)))

(total-with-tip 8.90 0.18) ;=> 11.3946999
(total-with-tip 50 0.18)   ;=> 64.015
```

### EXERCISE: Find per-person share of bill among a group

Modify our `total-with-tip` function, and call the new function `share-per-person`, that additionally takes in as an argument the number of people in the group for a bill.  Have the function return the average bill amount per person.

## Naming functions

Function names are symbols, just like the symbols we used with `def` when assigning names to values.

Symbols have to begin with a non-numeric character, and they can contain alphanumeric characters, along with *, +, !, -, _, and ?. This flexibility is important with functions, as there are certain idioms we use.

Functions that return true or false--called _predicates_--usually end in `?`:

* `zero?`
* `vector?`
* `empty?`

## Important functions

There are some functions that are essential when using Clojure. The arithmetic functions have already been covered. Let's look at some others.


### Collection functions

When we learned about data structures, we saw many functions that operated on those data structures, including:

* `count`
* `conj`
* `first`
* `rest`
* `nth`
* `assoc`
* `dissoc`
* `merge`

Some of the most powerful functions you can use with collections can take other functions as arguments. That's a complicated idea, so we'll learn more about that next.

## `map` and `reduce` - Functions that take other functions

One of the most magical things about Clojure--and many other programming languages--is that it can have functions that take other functions as arguments. That may not make sense at first, so let's look at an example:

```clj
(def dine-in-orders [12.40 18.95 23.81 19.95 12.40])
(def take-out-orders[6.00 6.00 7.95 6.25])

(map total-bill dine-in-orders)  ;=> [13.454 20.56075 25.833849999999998 21.64575 13.454]
(map total-bill take-out-orders) ;=> [6.51 6.51 8.62575 6.78125]
```

`map` is a function that takes another function, along with a collection. It calls the function provided to it on each member of the collection, then returns a new collection with the results of those function calls. This is a weird concept, but it is at the core of Clojure and functional programming in general.

Let's look at another function that takes a function. This one is `reduce`, and it is used to turn collections into a single value:

```clj
(defn add
  [x y]
  (+ x y))

(reduce add [1 2 3]) ;=> 6
```

`reduce` takes the first two members of the provided collection and calls the provided function with those members. Next, it calls the provided function again--this time, using the result of the previous function call, along with the next member of the collection. `reduce` does this over and over again until it finally reaches the end of the collection.

This process is complicated, so let's illustrate it further.

```clj
(def take-out-totals [6.51 6.51 8.62575 6.78125])

(reduce add take-out-totals) ;=> 28.427
```

In the example above, `reduce` calls `add` with the parameters `6.51` and `6.51`, returning `13.02`. Then, in order, it makes the following function calls:

* `(add 13.02    8.62575) ;=> 21.64575`
* `(add 21.64575 6.78125) ;=> 28.427`

### EXERCISE: Find the average

Create a function called `average` that takes a vector of bill amounts and returns the average of those amounts.

Hint: You will need to use `reduce` and `count`.

## Functions that return functions

Now that we have seen functions passed like any other value as an argument to a function, we can complete the functions-as-values symmetry by returning a function from a function.

```clj
(defn local-total-bill         ;; Define local-total-bill
  [tax-pct]                    ;;   with one argument.
  (fn                          ;; Create a new function
    [subtotal]                 ;;   with one argument.
    (+ subtotal                ;; Use both tax-pct and
      (* tax-pct subtotal))))  ;; subtotal in the calculation
```

In this code:

* Define `local-total-bill`, a function with one argument: `tax-pct`.
* `(fn ... )` is used inside of the function to create a new function.
* The new, inner function takes one argument: `subtotal`.
* The body of the new function uses both `subtotal` and `tax-pct` to calculate it's result.


Here's how `local-total-bill` works:

```clj
(def mpls-total-bill   (local-total-bill 0.07775))
(def stpaul-total-bill (local-total-bill 0.07625))

(mpls-total-bill 8.5)   ;; => 9.160875
(stpaul-total-bill 8.5) ;; => 9.148125
```

We define two new symbols `mpls-total-bill` and `stpaul-total-bill` to be the result of calling `local-total-bill` with different values for `tax-pct`.  Since `local-total-bill` returns a function, both of these names are now bound to functions, and each function holds onto the value of `tax-pct` that was present when it was created.

When the two new functions are called with a `subtotal` argument, they return different results based on the value of `tax-pct` that was used to create them.

### `partial`: Partial function arguments

The `partial` function is a general purpose version of our `local-total-bill` function.  When given a function `f` and a series of values, it will return a new function `g` that will call `f` with the previously provided arguments followed by the arguments passed to `g`.  This may sound confusing, but is easy to illustrate.

```clj
(defn total-bill
  [tax-pct subtotal]
  (+ subtotal
    (* tax-pct subtotal)))

(def mpls-total-bill   (partial total-bill 0.07775))
(def stpaul-total-bill (partial total-bill 0.07625))

(mpls-total-bill 8.5)   ;; => 9.160875
(stpaul-total-bill 8.5) ;; => 9.148125
```

In this code, we define a new version of `total-bill` that takes two arguments a `tax-pct` and a `subtotal`.  Using `partial` we create two functions with distinct values that will be used for the `tax-pct` argument.  Calling the `mpls-total-bill` function with a value of `8.5` is translated into a call to `total-bill` with arguments of `0.07775` and `8.5`.


### Composing functions with `comp`

An even more fundamental function building function is `comp`.  With `comp` we can compose a new function that applies each of a series of fuctions to the result of the previous function.  Once again, this can be easier to demonstrate than to explain.

```clj
(defn f [s] (str "F(" s ")"))
(defn g [s] (str "G(" s ")"))

(f "functional")   ;; => "F(functional)"
(g "composition")  ;; => "G(composition)"

(def f-o-g (comp f g))
(f-o-g "Clojure")  ;; => "F(G(Clojure))"

(f (g "Clojure"))  ;; => "F(G(Clojure))"
```

In the code above, we start be defining two seed functions, `f` and `g`.  Both of the functions return a string that wraps the argument `s` in a distinct label.  A third function `f-o-g` is created from `f` and `g` using `comp`.  When a value is pased to `f-o-g` the result shows that first `g` was called, because `G(...)` is the innermost wrapper, and the result of `(g "Clojure")` is passed to `f` producing `F(G(Clojure))`.  This is the same as the result of writing `(f (g "Clojure"))`.



### Next Step:

Go on to [**Module 5:** Flow Control](flow_control.md)

Back to [**Module 3:** Data Structures](data_structures.md)
