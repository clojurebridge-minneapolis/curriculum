More Functions
=========

* Important functions
  * Comparison (boolean) functions
  * String functions
* Anonymous functions

## Important functions

There are some functions that are essential when using Clojure. The arithmetic functions and `str` have already been covered, and you need to know them. Let's look at some others.

### Comparison (boolean) functions

You can use the function `=` to test the equality of things.  What it means to be equal depends on what the things are:

Numbers: 
```clj
(= 2 2)		;=> true
(= 2 2.0)	;=> false, a long and a double
``` 
Strings:
```clj
(= "hi" "hi")	;=> true
(= "Hi" "hi")	;=> false, caps matter
``` 
Sets:
```clj
(= #{2 3} #{3 2})	;=> true, order does not matter with sets
(= #{"hi"} #{"Hi"})	;=> false; each element must be equal and strings need the same case to be equal
``` 

An individual thing is always equal:
```clj
(= 2)	;=> true
```

But asking if nothing is equal is a mistake:
```clj
(=)	;=> ArityException Wrong number of args (0) passed to: core$-EQ-  clojure.lang.AFn.throwArity (AFn.java:437)
```

And more than two things can be compared but they must all be equal:
```clj
(= 2 2 2)	;=> true
(= 2 2 3)	;=> false
```

You can ask if things are not equal by using "not=" or wrapping an equals check in a "not":
```clj
(not= 2 2)	;=> false
(not (= 2 2))	;=> false
```

Some other comparison functions for numbers are `>`, `>=`, `<`, and `<=`.

```clj
(> 4 3)    ;=> true
(>= 4 5)   ;=> false
(< -1 1)   ;=> true
(<= -1 -2) ;=> false
```

### String functions

A large part of programming is manipulating strings. The most important string function in Clojure to remember is `str`, which concatenates all of its arguments into one string:

```clj
(str "Chocolate" ", " "strawberry" ", and " "vanilla")
;=> "Chocolate, strawberry, and vanilla"
```

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

Some of the most powerful functions you can use with collections can take other functions as arguments. That's a complicated idea, so we'll learn more about that soon.

### Naming functions

Often a program is easier to read when you give comparisons and other function calls a name.  Instead of simply checking:

```clj
(>= (:age person) 18)
```
consider defining that test as a function and using the function where you need to check.:

```clj
(defn legal-to-vote?
  "Is it legal for a person to vote?"
  [person]
  (>= (:age person) 18))
  ```

Here's another example, a function to test if someone is old enough to marry.

```clj
(defn legal-to-marry?
  "Is it legal for a person to marry?"
  [person]
  (>= (:age person) 18))
```

By giving the test an explicit name, it makes it easier to see what the program is trying to mean.  It also means that if the legal
voting age changes or there are other reasons a person might not be allowed to vote, you only have to change your program in one place.

By convention, functions that return booleans have names that end with `?`.

### Anonymous functions

So far, all the functions we've seen have had names, like `+` and `str` and `reduce`. However, functions don't need to have names, just like values don't need to have names. We call functions without names _anonymous functions_.

Before we go forward, you should understand that you can _always_ feel free to name your functions. There is nothing wrong at all with doing that. However, you _will_ see Clojure code with anonymous functions, so you should be able to understand it.

An anonymous function is created with `fn`, like so:

```clj
(fn [string1 string2] (str string1 " " string2))
```

You might notice that this function is the same as the function we called `join-with-space`. `fn` works a lot like `defn`; we still have arguments listed as a vector and a function body. I didn't break the line in the anonymous function above, but you can, just like you can in a named function.

Why would you ever do this? Anonymous functions can be very useful when we have functions that take other functions. Let's take each of our examples above, but use anonymous functions instead of named functions.

```clj
(map (fn [x] (* 3 x)) [1 2 3]) ;=> [3 6 9]
(reduce (fn [x y] (+ x y)) [1 2 3]) ;=> 6
(reduce
  (fn [s1 s2] (str s1 " " s2))
  ["i" "like" "peanut" "butter" "and" "jelly"])
;=> "i like peanut butter and jelly"
```
