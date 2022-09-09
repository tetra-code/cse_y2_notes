## Scala
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


- traits are like Interfaces

- Data classes are blueprints for immutable objects. Used to represent data records. Both languages implements equals (or __eq__) for them, s

Example:

    case class Address(street: sTring ;;)

they can instantiated repeateldy

- Pattern matching in scala