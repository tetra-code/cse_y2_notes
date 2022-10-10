# Side effect
**side effect** is when a function modifies some state outside of its scope or makes some noticeable change besides returning a value. The below modifies the value of variable max which is outside the function scope:

```
var max = -1

def greaterOrEqual(a: Int, b: Int): Boolean = {
    if(a >= b) {
        max = a // side effect!
        true
    } else {
    max = b // side effect!
    false
    }
}
```
possible fixes: 
- take it as input
- create a local scope of max
- deep copy

In functional programming, function should only return value and don't cause any operation outside of its scope.

General rule of thumb: if function returns nothing (void or Unit) then it it most likely does a side effect

Example of side effects are: 
- modifying a variable
- modify a data structrue in place; in FP data structures must be persistent
- setting a field on an object
- throwing an exception or halting with an error (in FP, types are used to encapsualte and propagate error behavior)
- print to the console, reading user input, reading writing to fields or screen (fp encapsulates external resouces into Monads)

# Pure function
**Pure function** is a function that only reads the declared inputs (not the outside scope variables) and doesn't create side effects. The below example only uses the input it has been passed; max as an input (although it never uses it)

```
def greaterOrEqual(a: Int, b: Int, max: Int): (Boolean, Int) = {
    if(a >= b) (true, a)
    else (false, b)
}
```

Pure functions also offer **referential transparency**, where expressions can be replaced with its corresponding value (and vice-versa) without changing the program's behavior. For y=f(x) we can replace f(x) with y wherever f(x) is used. 

```
var base = 10

def plus(a: Int): Int = {
  base + a
}
```

# No loops in FP
Imperative languages need loops because they have no other way to write iterative code. Loops are not functional as they can change and are mutable.

Functional languages can instead do recursion. 

Use mutable iterator variables in recursion

**Tail-recursive** functions are optimized by the Scala compiler.

```
def factorial(n: Int): Int = {
    @annotation.tailrec
    def factorialHelper(n: Int, acc: Int): Int =
        if (n <= 0) acc
        else factorialHelper(n-1, n*acc)

    factorialHelper(n, 1)
}
```

Functions like map, filter, and fold package up common recursive patterns into library functions that are easier to use than direct recursion and signal intent. 

# OO to FP
The below is flawed for FP as:

- charge() performs a side-effect: we need to contact the credit card company!
- buyCoffee() is not testable

```
class Cafe {
    def buyCoffee(cc: CreditCard): Coffee = {
        val cup = new Coffee()
        cc.charge(cup.price)
        cup
    }
}
```

Solutions is to decouple the action of buying coffee from charging. The following allows:
- Test
- Combine multiple Charges in one
- Maintain in flight accounts for all customers

```
class Cafe {
    def buyCoffee(cc: CreditCard): (Coffee, Charge) = {
        val cup = new Coffee()
        (cup, Charge(cc, cup.price))
    }
}

class Charge(cc: CreditCard, amount: Double) {
    def combine(other: Charge) = new Charge(cc, amount + other.amount)
    def pay = cc.charge(amount)
}
```

# Immutable data structures
FP data structures are operated on pure functions for immutable properties.

Scala has both mutable and immutable versions of many common data structures (lists, tuples, maps, and sets). If in doubt, use immutable.

```
val oneTwoThree = List(1, 2, 3)
val oneTwoThree_2 = 1 :: 2 :: 3 :: Nil  // same as the above
val one = oneTwoThree.head
val twoThree = oneTwoThree.tail
```

## List operators
One of the most used aspects (at least for this course) in Scala. Below is the most used and preferred operators for lists in Scala:

- **Nil** is a list which has zero elements in it.
- :: operator to add an element at beginning of the list 
- ::: add elements of list in front of this list

The '1 :: 2 :: 3 :: Nil' expression is the same as List(1, 2, 3).
The 'b :: a :: Nil' expression is same as List(b, a) 


In scala list
- *list.head* points to the first element of the list 
- *list.tail* is rest of the collection without its first element (NOT the last element of the list)

```
val oneItem = List(1)
oneItem.head                    // 1
oneItem.tail                    // Nil (empty list)

val mulItem = List(1, 2, 3, 4)
mulItem.head                    // 1
mulItem.tail is List(2, 3, 4)   // List(2, 3, 4)
```

## Pattern matching
**Pattern matching** checks a value against a pattern. If the match is successful, it can deconstruct a value into its consitituent parts. 

Below a list is deconstructed into a head (giving it the name x) and a tail/rest (giving it the name xs). Those two parts can now be accessed separately in the expression after the arrow.

```
def length(ints: List[Int]): Int = ints match {
    case Nil => 0
    case x :: xs => 1 + length(xs)     // x, xs are declared
}
```

Another example of pattern matching:
```
val x = List(1,2,3,4,5) match {
    case x :: 2 :: 4 :: xs => x
    case Nil => 42
    case x :: y :: 3 :: 4 :: xs => x + y
    case h :: t => h
    case _ => 404
}
```

Below are examples of recursion on data strucutures. Note they are not tail recursive:
```
def incrementAll(ints: List[Int]): List[Int] = ints match {
    case Nil => Nil
    case x :: xs => x + 1 :: incrementAll(xs)
}

def doubleAll(ints: List[Int]): List[Int] = ints match {
    case Nil => Nil
    case x :: xs => x * 2 :: doubleAll(xs)
}
```

<!-- Below won't compile though because we don't have the type *Any*, and there are more than one data types -->
The last default below is to make sure the data falls into at least one of the cases. 
```
val res = myvalue match {
    // Match on a value, like if
    case 1 => "One"
    // Match on the contents of a list
    case x :: xs => "The remaining contents are " + xs
    // Match on a case class, extract values
    case Email(addr, title, _) => s"New email: $title..."
    // Match on the type
    case xs : List[_] => "This is a list"
    // With a pattern guard
    case xs : List[Int] if xs.head == 5 => "This is a list of integers"
    case _ => "This is the default case"
}
```

## High-order function
**High-order function** is a function that accepts another function as its argument.

Useful higher-order functions include Map, filter, fold and friends as they package up common recursive patterns into library functions; implemented with recursion internally.


```
// Return elements that satisfy f
def filter(xs: List[A], f: A => Boolean) : List[A]
```

Below applyToAll takes two generics; type of A and type of B. These two generics don't have to be the same. One example being when dividing an integer by 2.

```
// see how you can implement below using tail recursion
def applyToAll[A, B](xs: List[A], f: A => B): List[B] = xs match {
    case Nil => Nil
    case h :: t => f(h) :: applyToAll(t, f)
}

// applyToAll in action
def incrementAll2(ints: List[Int]): List[Int] = applyToAll(ints, (x:Int) => x + 1)
def doubleAll2(ints: List[Int]): List[Int] = applyToAll(ints, (x:Int) => x * 2)
```

Some import higher-order functions:

`map(xs: List[A], f: A => B) : List[B]`             // apply function f to all elements and returns a new list.

`flatMap(xs: List[A], f: A => List[B]) : List[B]`   // Like map, but appends all the created lists from an element into one single list

`foldL(xs: List[A], f: (B, A) => B, init: B) : B`   // Takes function of 2 arguments and an init value and combines the elements by applying f on the result of each previous application. AKA reduce. init represents some value (like any)

`groupBy(xs: List[A], f: A => K): Map[K, List[A]]`  // Partitions xs into a map of traversable collections according to a discriminator function.

`filter(xs: List[A], f: A => Boolean) : List[A]`    // Takes a predicate f and returns all elements that satisfy it

`scanL(xs: List[A], f: (B, A) => B, init: B) : List[B]` // Like foldL, but returns a list of all intermediate results

`zip(xs: List[A], ys: List[B]): List[(A,B)]`        // Returns an iterable collection formed by iterating over the corresponding items of xs and ys.

# Laziness
**Laziness** is an evaluation strategy which delays the evaluation of an expression until its value is needed. Here you acutally use the keyword *lazy*

Allows to separate defining how you evaluate a value from when you actually evaluate it.

```
    lazy val x = { println("Evaluates x "); 1}  
    val y = { println("Evaluates y"); 2}
    x + y

    // Output:
    // Evaluates y
    // Evaluates x 
```

Laziness is useful for
- separating pipleine construction form its evaluation
- No need to read datasets in memory (process in lazy loaded batches)
- Generate infinite collections
- optimize executions plans

```
    val r1 = (1 to 3)
    .map(x => {
      println("Mapping")
      x + 1
    })
    .filter( x => {
      println("Filtering")
      x % 2 == 0
    })
    .sum

    //Output:
    // Mapping
    // Mapping
    // Mapping
    // Filtering
    // Filtering
    // Filtering
```

```
      // Working on a lazy list
  val r2 = (1 to 3).to(LazyList)
    .map(x => {
      println("Mapping")
      x + 1
    })
    .filter( x => {
      println("Filtering")
      x % 2 == 0
    })
    .sum

```


Scala lists, maps are eager to output as fast as possible. Lazy lists help form lazy pipelines. In tools like Spark and Flink, we always express computations in a lazy manner. This allows for optimizations before the actual computation is executed

# Monads
design pattern to define how functions can be used togeter to build generic types.
Practically, a monad is a value-wrapping type that:
* Has an identity function * Has a flatMap function, that allows data to be transferred between monad types
In fp, we want ot return the effect and not mutate with it. 
In Monad, we wrap the value to keep the effect.

Exceptions are considered side-effect because they hold the state of the problem. E  . Exceptions are not type-safe as they don't return the expected type(we might have expected a double but instead got a exception). So instead of indicating the type to the expected type, *map the effects to the type system using monads*. 

We can use several monads:

- Option: wraps a value that may be null or undefined. Option can have either 2 instances: Some[A], where A represents the type of the value
- Try: wraps t
- Future: 


## Combine monad with flatMap
flatMap allows us to join sequences of arbitrary types. 


In SCala, object keyword is a singleton and a class is the template to be able to generate more


In the Amazon example: login returns Future[Amazon] which we then use flatMap with Future[Amazon].search, which returns a Future[seq[string]]. THerefore type of result is a Future[seq[string]]. In Scala, declaration of the value of Future[T] is Option[Try[T]]. THus result.value is Option[Try[Seq[String]]]

# Big datasets
In a big data system:
- client code processes data
- a data source is a container a data


Iteration

In the context of BDP, iteration allows us to process finite-sized data sets without loading them in memory at once.

## Conversion or Mapping