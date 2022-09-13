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

### Defining objects

