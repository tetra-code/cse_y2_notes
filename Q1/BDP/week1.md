# Week 1

# Scala 

## Basics
- Scala is both Object Oriented and Functional programming

- Type inference is used extensively

- Types are automatic but not dynamic

    val a: Int = 5
    val b = 5
    b = 6

- *vals* are single assignment, not mutable after assignment
- *vars* allow multiple assignments
- Statically typed
- Evaluated expressions have types
- Return type is the most generic type of all return expressions. If there is no return keyword or the type is not indiciated, the compiler infers the last expression is taken to be the return value.

- IF a function doesn't return a value, it means it is changing the state, does no longer functional programming.

- IN higher-order functions, the Scala compiler checks whether the argument type and the signature matches

- In Scala, val means a read-only attribute while var is a read-write
a default constructor is created automatically

EXample of OOP:

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

- traits are like Interfaces

- Data classes are blueprints for immutable objects. Used to represent data records. Both languages implements equals (or __eq__) for them, s

Example:

    case class Address(street: sTring ;;)

they can instantiated repeateldy

- The class *Any* is like the class *Object* in Java, it is the root of the Scala class hierarchy.

- Pattern matching in scala
Below won't compile though because we don't have the type *Any*, and there are more than one data types

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

The last default is to make sure the data falls into at least one of them. 

- In functional programming we use recursion not loops. THis is because loops are not functional as they can change and are mutable.

## Data structures
Different types of data structure:

- *Unstrucutred data*: format is not known such as text documents or HTML pages. Not searchable
- *Semi-strutured*: data with a known format like JSON, CSV, XML. Needs some process to read it
- *Structured*: data with known format linked together in graphs or tables like SQL or Graph databases.

To be classified as structured, it requries some type of schema. 

Data frequently transferred between different systems. If the data is structured, the other system may need to install additional software to read that structured data. Semi-structured or unstructured data is easier to maintain and easier to move around with it. 
### List
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


### Set
Set stores values, without any order or repeated values

```
    val s = Set(1, 2, 3, 4, 4)
    s: scala.collection.immutable.Set[Int] = Set(1, 2, 3, 4)
```

### Map or Dictionaries
Maps or Dictionaries or Associate Arrays ia a collection of (k, v) pairs. k must be unique. One key always corresponds to one value. We can choose between Hashmap or Treemap, where accessing takes O(1) for hashmap and O(n) for treemap. In BDP, hashmap is often preferred due to speed and no need to sort the keys all across the database.

```
    scala> val m = Map(("a" -> 1), ("b" -> 2)) 
    val m: scala.collection.immutable.Map[String,Int] = Map(a -> 1, b -> 2)
```

*In  a set you can have multiple entries for the same key while in map you can only have one entry for one key

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
    case class Node(id: Int, attributes: Map[A, B])
    case class Edge(a: Node, b: Node, directed: Option[Boolean],
                    weight: Option[Double] )
```

Here the directed has Option[boolean] because the edge might not know whether the graph is directed or undirected, in which case neither false nor true.

## Tree (nested data type)
Trees are a subset of a graph. It is an ordered graph without loops (acyclic). Parsing a JSON file in almost any language will result in a series of nested maps, which is a tree:

```
    Map("id" -> 5542101946L,
        "type" -> "PushEvent",
        "actor" -> Map("id" -> 801183.0, "login" -> "tvansteenburgh"),
        "repo" -> Map("id" -> 4.2362423E7, "name" -> "juju-solutions/review-queue"))
    )
```

## Tuples
An n-tuple is a list of n elements of certain types. The different types must be known beforehand

```
    val record = Tuple4[Int, String, String, Int] (1, "Matt", "Damon", 1970)
    // alternatively
    val record = (1, "Matt", "Damon", 1970)
```

Scala makes it easy to declare and use tuples by automatically inferring the types of the tuple contents.

```
    val a = (1, ("Foo", 2)) // type: Tuple2[Int, Tuple2[String, Int]]
    // alternatively: (Int, (String, Int))

    println(a._1) // prints 1
    println(a._2._1) // prints Foo
```

The **._**

## Relations
A relation is a Set of n-tuples, which must be of the same type. 

The same RDBS rules apply here (in fact it is derived from here):
- a tuple can have different data types but all the tuples of a set must have the same data type and in same order. 
- One of the tuple elements denotes a key and a key can't be repeated
- Typical operations on relations are insert, remove and join. Join allows us to compute new relations by joining existing ones on common fields.

```
    val movie1 = (1, "Martian", 2015, 2)
    val movie2 = (2, "Prometheus", 2012, 2)
    val movie3 = (3, "2001: Space Odyssey", 1968, 1)

    val movies = Set(movie1, movie2, movie3)

    val stanley = (1, "Stanley Kubrick", 1928)
    val ridley = (2, "Ridley Scott", 1937)

    val directors = Set(stanley, ridley)
```
From above: How can we get the list of movie names together with the name of their directors? 

*In BDP, we normally create some case class and create tuples from those classes as data. It is easier to read from input data and process.

EXplicit Join operation doesn't exist in Scala.