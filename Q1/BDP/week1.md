# Basic data structures
Different types of data structure:

- *Unstrucutred data*: format is not known such as text documents or HTML pages. Not searchable
- *Semi-strutured*: data with a known format like JSON, CSV, XML. Needs some process to read it
- *Structured*: data with known format linked together in graphs or tables like SQL or Graph databases.

To be classified as structured, it requries some type of schema. 

Data frequently transferred between different systems. If the data is structured, the other system may need to install additional software to read that structured data. Semi-structured or unstructured data is easier to maintain and easier to move around with it. 

## List
In functional programming a List is more common than an Array.

```
val l = List(1, 2, 3, 4)
```

Basic properties:
- Size is bounded by memory
- Items can be accessed by an index
- Items can only be insereted at the end (append) or beginning (prepend)
- Can be sorted
- Immutable

If you want to insert an item, you must copy the list into a new list and then add the new item at the end. Because this can take a lot of memory, we don't deep copy, just add the new item and add a reference

Remember a list is a link of references. If you need to add item in the middle, then scala will deep copy and generate an entirely new list.

For this reason, in scala it is almost always prepend for the shallow copying and referencing while in large file systems it is almost always appends.

## Set
Set stores values, without any order or repeated values

```
val s = Set(1, 2, 3, 4, 4)
s: scala.collection.immutable.Set[Int] = Set(1, 2, 3, 4)
```

## Map or Dictionaries
Maps or Dictionaries or Associate Arrays ia a collection of (k, v) pairs. 

k must be unique. 

One key always corresponds to one value. We can choose between Hashmap or Treemap, where accessing takes O(1) for hashmap and O(n) for treemap. 

In BDP, hashmap is often preferred due to speed and no need to sort the keys all across the database.

```
scala> val m = Map(("a" -> 1), ("b" -> 2)) 
val m: scala.collection.immutable.Map[String,Int] = Map(a -> 1, b -> 2)
```

*In a set you can have multiple entries for the same key while in map you can only have one entry for one key*
```
    // Won't work
    Set(("a", 1), ("a", 2)).toMap
    
    // Will work
    Set(("a", 1), ("b", 2)).toMap
```

## Graph (nested data type)
A graph data structure consists of 
- a finite set of vertices or nodes
- a set of unordered pairs of these vertices for an undirected graph or a set of ordered pairs for a directed graph.

The nodes can contain attributes and edges can contain weights and directions.

```
case class Node(
    id: Int, 
    attributes: Map[A, B]
)
case class Edge(
    a: Node, 
    b: Node, 
    directed: Option[Boolean],
    weight: Option[Double]
)
```

*Above the directed has Option[boolean] because the edge might not know whether the graph is directed or undirected, in which case neither false nor true.

## Tree (nested data type)
Trees are a subset of a graph. It is an *ordered graph without loops* (acyclic). 

Example of a tree:
```
a = {
    "id": "5542101946", 
    "type": "PushEvent",
    "actor": {
      "id": 801183,
      "login": "tvansteenburgh"
    },
    "repo": {
      "id": 42362423,
      "name": "juju-solutions/review-queue"
   }
}
```

Parsing the above JSON in almost any language will result in a series of nested maps:
```
Map("id" -> 5542101946L,
    "type" -> "PushEvent",
    "actor" -> Map("id" -> 801183.0, "login" -> "tvansteenburgh"),
    "repo" -> Map("id" -> 4.2362423E7, "name" -> "juju-solutions/review-queue"))
)
```

## Tuples
An n-tuple is a list of n elements of different types. The different types must be known beforehand
```
val record = Tuple4[Int, String, String, Int] (1, "Matt", "Damon", 1970)
// alternatively
val record = (1, "Matt", "Damon", 1970)
```

Scala uses the **._** operator to access elements inside the tuple
```
val a = (1, ("Foo", 2)) // type: Tuple2[Int, Tuple2[String, Int]]
println(a._1) // prints 1
println(a._2._1) // prints Foo
```

## Relations
A relation is a set of n-tuples, where *each tuple must have the same order of different data types*

The same RDBS rules apply here (in fact it is derived from here):
- a tuple can have different data types but all the tuples of a set must have the same data type and in same order. 
- One of the tuple elements denotes a key and a key can't be repeated
- Typical operations on relations are insert, remove and join

```
val movie1 = (1, "Martian", 2015, 2)
val movie2 = (2, "Prometheus", 2012, 2)
val movie3 = (3, "2001: Space Odyssey", 1968, 1)

val movies = Set(movie1, movie2, movie3)

val stanley = (1, "Stanley Kubrick", 1928)
val ridley = (2, "Ridley Scott", 1937)

val directors = Set(stanley, ridley)
```

Explicit Join operation doesn't exist in Scala.

## Key/Value pairs
A key/value pair (or K/V) is a more general type of a relation, where *each key can appear more than once*.

```
// We assume that the first Tuple element represents the key
val a = (1, ("Martian", 2015))
val b = (1, ("Prometheus", 2012))

val kv = List(a, b)
// type: List[(Int, (String, Int))]
```

Another way to represent K/V pairs is with a map:

```
val xs = Map(1 -> List(("Martian", 2015), ("Prometheus", 2012)))
// type: Map[Int, List[(String, Int)]]
```

K and V are flexible: that’s why the Key/Value abstraction is key to NoSQL databases, including MongoDB, DynamoDB, Redis etc

# FP: Basics
**Imperative programming**:
- uses statements to change program's state.
- focus on how program executes (process)
- programs are series of instructions or statements

**Functional programming**:
- uses functions to perform everything
- focus on what programs to execute (results)
- programs are like mathematical structures

Functional programming characteristics:
- Absence of side-effects: a function, given an argument, always returns the same results irrespective of and without modifying its environment.
- Immutable data structures: side-effect free functions operate on immutable data.
- Higher-order functions: functions can take functions as arguments to parametrize their behavior. 
- Laziness: compute only when needed and wait until then
- no need for loops: instead uses recursion or high order functions.

# Scala: Basics
Scala is both Object Oriented and Functional programming.

```
val a: Int = 5
val b = 5
b = 6
```

Scala properties:
- **vals** are single assignment, not mutable after assignment (read only)
- **vars** allow multiple assignments, thus mutable (read-write)
- Statically typed, not dynamic
- Evaluated expressions have types
- Return type is the most generic type of all return expressions. If there is no return keyword or the type is not indiciated, the compiler infers the last expression is taken to be the return value.
- class **Any** is like the class *Object* in Java; root of the Scala class hierarchy.
- if a function doesn't return a value, it means it is changing the state (does no longer functional programming).
- higher-order functions, the Scala compiler checks whether the argument type and the signature matches.
- a default constructor is created automatically.

Example of a case class in Scala:
```
case class Address(street: sTring ;;)
```

Example of Scala OOP:
```
class Foo(
    val x: Int, 
    var y: Double = 0.0
)

class Bar(
    x: INt, 
    y: INt, 
    z: int
) extends Foo(x, y)

trait Printable {
    val s: String
    def asString(): STring
}

class Baz (x: INt, y: DOuble, private val z: Int) extends Foo(x,y) with Printable {
    override val s: String= "Bazz"
    override def asString(): String = ???
}
```

## Object vs Class
In Scala the **object** keyword is a **singleton** and a **class** is a template to be able to generate more. 
```
object Converter extends App {
    def toInt(a: String): Try[Int] = Try{Integer.parseInt(a)}
    def toString(b: Int): Try[String] = Try{b.toString}

    val a = toInt("4").flatMap(x => toString(x))
    println(a)

    val b = toInt("foo").flatMap(x => toString(x))
    println(b)
}
```

## Trait
**Trait** is like an interface

It has a collection of abstract and non-abstract methods

## Case
**case** class is like a blueprint for immutable objects: used to represent data records.

## For loops
For loops do exist in scala. The **<-** operator is a reserved as an iterator for the for loop.

Example of nested for loop:
```
for { 
    x <- 1 to 5
    y <- 2 to 6
} println (x,y)

//result
(1,2)
(1,3)
(1,4)
(1,5)
(1,6)
(2,2)
(2,3)
...
(5,5)
(5,6)
```