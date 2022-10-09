# Scala with Spark
Here we will use spark

## Map/Reduce concept

Use map/reduce to solve the distributed system algorithm


    map(List[(K1, V1)], f: (K1, V1) -> (K2, V2)): List[(K2, V2)]
    reduce((K2, List[V2])): List[(K3, V3)]

Map phase and Reduce phase are both done in parallel.

It is fault tolerant but lacks performance:

- Before each Map or reduce step, there are these shuffling and iterative writes that is siginificant latency
- Hard to express iterative problems M/R. Not generalaible to all possible problems (good for word count). If we want to iterate something, the whole cycle has to be repeated.

Some solution is to save the shuffling in memory. 


## DryadLINQ

## FlumeJava
Not only MapReduce but programmer write his own construct. In practice, not very easy to work with. Functional programming Scala is much better

## Spark
New technology (created in 2009). Originally most systems are built around acyclic (not iterable). Spark is open source cluster computing framework. While spark itself is implemented in Scala. It uses the 'Akka' actor framework to handle distributed state and Netty to handle networking. 

Also available in Python to allow python programs to acces java objects in a remote JVM. The PySpark PI is designed to do most compuations in the remote JVM, if processing needs to happen IN Python



### RDD (Resilient Distributed Dataset)
RDDs are the core abstraction that Spark uses.
Huge dataset (immutable). Reside mostly in memory

First create spark context (sc). Turn this into a RDD, and use this RDD to apply functional proramming. All these transformations (take, foreach, reducebykey) create a NEW RDD

To create an RDD:
- read data from exetrnal source
- convert a local memory dataset to distributed one
- Transform existing RDD

In RDD:
- Transformation: Applying a function that returns a new RDD. They are lazy.
- Action: Request the computation of a result. They are eager.

In Scala, lists are eager but in RDD is lazy by default

```
// This just sets up the pipeline
val result = rdd
  .flatMap(l => l.split(" "))
  .map(x => (x, 1))

// Side-effect, triggers computation
result.foreach(println)
```

Some common RDD transformation:
- map
- flatmap
- filter 
- split

We can't have a nested RDD of RDDs; only an array of RDDs.

Before action, we create a pipeline. With action, we actually perform the computation (lazy)

WIth transform, we create new RDDs but it is not like a new RDD on top of another in the memory. When the action is executed, Spark optimizes the RDDs so won't have to worry about memory overflow.


Common actions on RDDs:
- collect
- take
- reduce, fold
- aggregate (new in Spark)


aggregate vs fold: types are different. With aggregate tye type may be different etween A and B, with fold they are all the same type.

With transformation we create new RDD and with an action we finally compute the RDD (no more RDD) 