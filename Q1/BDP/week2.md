# Week 2

## Key/Value pairs
Key value is used often in BDP because of ease of use.

## Functional programming 

### Basics
Basically programs are constructed by applying and composing functions

At heart, an imperative program is a set of statements that are executed one after another (iterative). Similarly, a functional program is a complex expression made up of functions that's simplified by applying those functions.

*side effect* is when a function modifies some state outside of its scope or makes some noticeable change besides returning a value. The below modifies the value of variable max:

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
possible fixes: take it as input, create a local scope of max, deep copy, or return a pair of boolean and the max. In addition the function above does more than one function (reassigning max's value). 

In functional programming the only thing a function should do is return a value and dont' cause any operation outside of its scope. General rule of thumb: if something is not a NOT-OPERATION, then it is a side effect

Example of side effects are: 
- modifying a variable
- modify a data structrue in place; must be persistent
- setting a field on a n object
- throwwng an exception or halting with an error (in Fp we use types that encapsualte and propgae erron behavior)
- print to the console, reading user input, reading writing to fiels or screen (fp encapsulates external resouces into Monads)
read(print), write, a lot of IO functions.

*pure function* is a function that only reads the declared inputs (not the outside scope variables) and doesn't create side effects. The below example only uses the input it has been passed; max as an input (although it never uses it)

```
    def greaterOrEqual(a: Int, b: Int, max: Int): (Boolean, Int) = {
        if(a >= b) (true, a)
        else (false, b)
    }
```

pure functions also offer *referential transparency*, where expressions can be replaced with its corresponding value (and vice-versa) without changing the program's behavior. For y=f(x) we can replace f(x) with y whereever f(x) is used. 

*high order function* 
The following function signature:
```
    f(x:[A], y:(z:A) -> B) -> [B]
```

Takes a traversable A, takes each element of A and does some function and converts into element of of B and returns a new traversable of B. This is an example of high order function, as the parameter y is an argument

Functional programming characteristics:
- Absence of side-effects: a function, given an argument, always returns the same results irrespective of and without modifying its environment.
- Immutable data structures: Side-effect free functions operate on immutable data.
- Higher-order functions: Functions can take functions as arguments to parametrize their behavior. 
- Laziness: The art of waiting to compute till you can wait no more
- no need for loops. It uses instead recursion or high order functions.

### No loops in FP

*Imperative languages need loops because they have no other way to write iterative code. Functional languages do: recursion. 

Useful higher-order functions include Map, filter, fold and friends as they package up common recursive patterns into library functions; implemented with recursion internally.

not loops with mutable iterator variables (like for-loop or while-loop). higher-order functions come in. Map, filter, fold and friends package up common recursive patterns into library functions that are easier to use than direct recursion and signal intent. 

### OO to FP
** come back to this again when reviewing
The below is flawed for FP as it has side effects and the function is not testable.

```
    class Cafe {
        def buyCoffee(cc: CreditCard): Coffee = {
            val cup = new Coffee()
            cc.charge(cup.price)
            cup
        }
    }
```

Solutions is to separate and break the eternal dependencies.

```
    class Cafe {
        def buyCoffee(cc: CreditCard, p: Payments): Coffee = {
            val cup = new Coffee()
            p.charge(cc, cup.price)
            cup
        }
    }
```

THe for loop the 

## Immutable data structures
Scala has both mutable and immutable versions of many common data structures. If in doubt, use immutable.

```
  val oneTwoThree = List(1, 2, 3)
  val oneTwoThree_2 = 1 :: 2 :: 3 :: Nil  // same as the above
  val one = oneTwoThree.head
  val twoThree = oneTwoThree.tail
```

*Nil* is a list which has zero elements in it.

The ::() operator in is utilized to add an element to the beginning of the list. 

The '1 :: 2 :: 3 :: Nil' expression is the same as List(1, 2, 3).
The 'b :: a :: Nil' expression is same as List(b, a) 

In scala list, *list.head* points to the first element of the list but *list.tail* does NOT point to the last element of the list:

```
    val oneItem = List(1)
    oneItem.head                    // 1
    oneItem.tail                    // Nil (empty list)

    val mulItem = List(1, 2, 3, 4)
    mulItem.head                    // 1
    mulItem.tail is List(2, 3, 4)   // List(2, 3, 4)

    b::a::Nil 
```

*Pattern matching* checks a value against a pattern. If the match is successful, it can deconstruct a value into its consitituent parts. Below a list is deconstructed into a head (giving it the name x) and a tail/rest (giving it the name xs). Those two parts can now be accessed separately in the expression after the arrow.

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

### Higher-order function
As mentioned before, higher-order function is a function that can accept a function as an argument or returns a function.

```
    // Return elements that satisfy f
    def filter(xs: List[A], f: A => Boolean) : List[A]
```


Below applyToAll takes two generics; type of A and type of B. These two generics don't have to be the same. One example being when dividing an integer by 2.

```
// *see how you can implement below using tail recursion
def applyToAll[A, B](xs: List[A], f: A => B): List[B] = xs match {
    case Nil => Nil
    case h :: t => f(h) :: applyToAll(t, f)
}

// applyToAll in action
def incrementAll2(ints: List[Int]): List[Int] = applyToAll(ints, (x:Int) => x + 1)
def doubleAll2(ints: List[Int]): List[Int] = applyToAll(ints, (x:Int) => x * 2)
```

Some import higher-order functions:
- map(xs: List[A], f: A => B) : List[B]             // f to all elements and returns a new list.
- flatMap(xs: List[A], f: A => List[B]) : List[B]   // Like map, but flattens the result to a single list.
- foldL(xs: List[A], f: (B, A) => B, init: B) : B   // Takes function of 2 arguments and an init value and combines the elements by applying f on the result of each previous application. AKA reduce. init represents some value (like any)

*flatMap appends all the created lists from an element into one single list

## Laziness
Laziness is an evaluation strategy which delays the evaluation of an expression until its value is needed. Here you acutally use the keyword *lazy*

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
    //Filtering
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

## Monads
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

## Big datasets
In a big data system:
- client code processes data
- a data source is a container a data


## Conversino or Mapping