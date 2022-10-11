# Parallelism meets data processing
Parallelism is about speeding up computations by using multiple processors.

- **Task parallelism**: Different computations performed on the same data
- **Data parallelism**: Apply the same computation on dataset partitions

BDP in a distributed setting is hard due to:
- Latency: accessing data is 1000x slower on disk and in network 1000000x slower than accessing data in memory
- Partial failure: 100s of machines may fail at any time

## Map/Reduce model
The **map/reduce model** is a programming model for processing big data sets with a parallel, distributed algorithm on a cluster. It has a **map phase** and **reduce phase** and both done in parallel.

```
map(List[(K1, V1)], f: (K1, V1) -> (K2, V2)): List[(K2, V2)]
reduce((K2, List[V2])): List[(K3, V3)]
```

![Image](../../images/map_reduce.png)

- DFS chunks are assigned to Map tasks processing each chunk into a sequence of KV pairs.
- Periodically, the buffered pairs are written to local disk.
- The keys are divided among all Reduce tasks.
- Reduce tasks work on each key separately and combine all the values associated with a specific key.

## Hadoop Map/Reduce

![Image](../../images/hadoop_map_reduce.png)

The above model is fault tolerant but lacks performance:
- Before each Map and Reduce phase, there are these shuffling and iterative writes; significant latency
- If a problem requires iteration, the whole cycle has to be repeated. Thus hard to express iterative problems in M/R

## DryadLINQ

![Image](../../images/DryadLINQ.png)

## FlumeJava
From Google

Not only Map/Reduce provides other simple abstractions for programming data-parallel computations. In practice, not very easy to work with.

```
PTable<String,Integer> wordsWithOnes =
  words.parallelDo( new DoFn<String, Pair<String,Integer>>() {
    void process(String word,
                  EmitFn<Pair<String,Integer>> emitFn) {
      emitFn.emit(Pair.of(word, 1));
    }
  }, tableOf(strings(), ints()));

PTable<String,Collection<Integer>>
  groupedWordsWithOnes = wordsWithOnes.groupByKey();

PTable<String,Integer> wordCounts =
  groupedWordsWithOnes.combineValues(SUM_INTS);
```

# Spark
Relatively new technology (created in 2009). 

Originally most systems are built around acyclic (not iterable). 

Spark is open source cluster computing framework. 

It uses the **Akka** actor framework to handle distributed state and **Netty** to handle networking. 

- automates distribution of data and computations on a cluster of computers
- provides a fault-tolerant abstraction to distributed datasets
- is based on functional programming primitives
- provides two abstractions to data, list-like (**RDD**) and table-like (**Dataset**)

Also available in Python to allow python programs to acces java objects in a remote JVM. The PySpark PI is designed to do most compuations in the remote JVM, if processing needs to happen IN Python

# RDD (Resilient Distributed Dataset)
**RDD** is the core abstraction that Spark uses.

RDD makes datasets that are distributed over a cluster of machines look like a Scala collection.

RDD properties:
- immutable
- reside mostly in memory (fast)
- transparently distributed
- features alL FP programming primities

How spark uses RDD:
1. create spark context (sc)
2. this into a RDD
3. use this RDD to apply functional programming. 

To create an RDD:
- read data from external source
```
val rdd1 = sc.textFile("hdfs://...")
val rdd2 = sc.textFile("file://odyssey.txt")
val rdd3 = sc.textFile("s3://...")
```
- convert a local memory dataset to distributed one
```
val xs: Range = Range(1, 10000)
val rdd: RDD[Int] = sc.parallelize(xs)
```
- apply transformation operation onto an existing RDD
```
rdd.map(x => x.toString) //returns an RDD[String]
```

Two types of oeprations in RDD:
- **Transformation**: Applying a function that returns a new RDD. They are lazy. 
- **Action**: Request the computation of a result. They are eager.

With transformation we create new RDD and with an action we finally compute the RDD (no more RDD).

With transform, we create new RDDs but it is not like a new RDD on top of another in the memory: when the action is executed, Spark optimizes the RDDs so won't have to worry about memory overflow.

Before action, we create a pipeline. With action, we actually perform the computation (lazy)

In Scala, lists are eager but in RDD is lazy by default:
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
- sortBy
- filter 
- take
- split

Common actions on RDDs:
- collect
- take
- reduce, fold
- foreach
- aggregate (new in Spark)

aggregate vs fold: types are different. With aggregate, the type may be different between A and B, with fold they are all the same type.

## Pair RDDs
We can't have a nested RDD of RDDs; only an array of RDDs. But it can represent any complex data type, if it can be iterated. 

The type **RDD[(K, V)]** is called **PairRDDs** and it can be both iterated and indexed.

We can only combine RDDs if their contents can be indexed. Therefore the **join** operation is only defined on PairRDDs.

Create PairRDDs from regular RDD by simply applying an indexing function:

```
val rdd = sc.parallelize(List("foo", "bar", "baz")) // RDD[String]

val pairRDD = rdd.map(x => (x.charAt(0), x))  // RDD[(Char, String)]
pairRDD.collect // Array((f,foo), (b,bar), (b,baz))

val pairRDD2 = rdd.keyBy(x => x.toLowerCase.head) // RDD[(Char, String)]
pairRDD2.collect // Array((f,foo), (b,bar), (b,baz))

val pairRDD3 = rdd.groupBy(x => x.charAt(0))  // RDD[(Char, Iterable(String))]
pairRDD3.collect // Array((b,CompactBuffer(bar, baz)), (f,CompactBuffer(foo)))
```

There are PairRDD specific transformations:
- **reduceByKey**: merge the values for each key using an associative and commutative reduce function
- **aggregateByKey**: aggregate the values of each key, using given combine functions and a neutral “zero value”
- **join**: return an RDD containing all pairs of elements with matching keys

```
val odyssey = sc.textFile("sample.txt").flatMap(_.split(" "))
val words = odyssey.flatMap(_.split(" ")).map(c => (c, 1))


val counts = words.groupByKey()         // RDD[(String, Iterable[Int])]
  .map(row => (row._1, row._2.sum))     // RDD[(String, Int)]
  .collect()                            // Array[(String, Int)]

val counts2 = words.reduceByKey(_ + _)  // RDD[(String, Int)]
  .collect()                            // Array[(String, Int)]

val counts3 = words.aggregateByKey(0)(_ + _, _ + _) // RDD[(String, Int)]
```

More example of aggregateByKey
```
object PartOfSpeech {
  sealed trait Word
  case object Verb extends Word
  case object Noun extends Word
  case object Article extends Word
  case object Other extends Word
}

def partOfSpeech(w: String): Word = ???

odyssey.groupBy(partOfSpeech)
      .aggregateByKey(0)((acc, x) => acc + 1,
                          (x, y) => x + y)
```

Example of join:
```
case class Person(
  id: Int, 
  name: String
)
case class Addr(
  id: Int, 
  person_id: Int,
  address: String, 
  number: Int
)

val pers = sc.textFile("pers.csv") // id, name
val addr = sc.textFile("addr.csv") // id, person_id, street, num

val ps = pers.map(_.split(",")).map(
  x => Person(
    x(0).toInt, 
    x(1)
  )
)
val as = addr.map(_.split(",")).map(
  x => Addr(
    x(0).toInt, 
    x(1).toInt,
    x(2), 
    x(3).toInt
  )
)

val pairPs = ps.keyBy(_.id) // RDD[(Int, Person)]
val pairAs = as.keyBy(_.person_id) // RDD[(Int, Addr)]

val addrForPers = pairAs.join(pairPs) // RDD[(Int, (Addr, Person))]

```

Different type of join:

![Image](../../images/join_types.jpg)

## RDD partitions
Each RDD has five main properties
- list of partitions
- function for computing each split
- list of dependencies on other RDDs
- (optional) partitioner for key-value RDDs
- (optional) list of preferred locations to compute each split on

**Partitions** define a unit of computation and persistence: any Spark computation transforms a partition to a new partition.

Data in RDDs is split into partitions, not as a continuous memory block spread across a cluster.

If during computation a machine fails, Spark will redistribute its partitions to other machines and restart the computation on those partitions only.

The partitioning scheme of an application is configurable; better configurations lead to better performance.

Spark supports 3 types of partitioning.
For all RDD:
- **Default partitioning**: split in equal sized partitions, no knowledge of underlying data properties

For PairRDD only:
- **Range partitioning**: uses natural order of keys to split the dataset in required number of partitions. *keys must be naturally ordered and equally distributed across the value range.
- **Hash partitioning**: Calculates a hash over each item key and then produces the modulo of this hash to determine the new partition. This is equivalent to:

```
key.hashCode() % numPartitions
```

Depending on the previous transformation operation, the partition dependencies also vary:
- **Narrow dependencies**: Each partition of the source RDD is used by *at most one partition of the target RDD*
- **Wide dependencies**: a partition of the source RDD is used in multiple partitions of the target RDD

![Image](../../images/partition_dependencies.png)

### Shuffling
When operations need to calculate results using a common characteristic (e.g. a common key), this data needs to reside on the same physical node. This is the case with *all “wide dependency” operations*. The process of re-arranging data so that similar records end up in the same partitions is called **shuffling**.

Shuffling is very expensive as it involves moving data across the network and possibly spilling them to disk (e.g. if too much data is computed to be hosted on a single node). Avoid it at all costs!

![Image](../../images/shuffling.png)

To minimize shuffling:

![Image](../../images/minimize_shuffling.png)

## Lineage
RDDs create a directed acyclic graph of computations.

Lineage information allow an RDD to be traced to its ancestors.

The following code:

```
val rdd1 = sc.parallelize(0 to 10)
val rdd2 = sc.parallelize(10 to 100)
val rdd3 = rdd1.cartesian(rdd2)
val rdd4 = rdd3.map(x => (x._1 + 1, x._2 + 1))
```

will result in the following log:

```
scala> rdd4.toDebugString
res3: String =
(16) MapPartitionsRDD[3] at map at <console>:30 []
 |   CartesianRDD[2] at cartesian at <console>:28 []
 |   ParallelCollectionRDD[0] at parallelize at <console>:24 []
 |   ParallelCollectionRDD[1] at parallelize at <console>:24 []
```

## Persistence
RDD stores data in three ways
- Java objects: each item in the RDD is an allocated object
- serialized data: memory-efficient formats. Faster option if sending data across network or writing to disk
- on filesystem: if RDD too big mapped on a filesystem (usually HDFS)

Frequently used computations (along with its data) can be configured to be stored in memory. Below *persisted* is now cached. Further accesses will avoid reloading the textfile and applying the map function.

```
val rdd = sc.textFile("hdfs://host/foo.txt")
val persisted = rdd.map(x => x + 1).persist(StorageLevel.MEMORY_ONLY_SER)
```

Different types of persistence storage levels:
- **MEMORY_ONLY**: default level. RDD saved as deserialized Java object in JVM. If RDD doesn't fit in memory, some partitions not cached.
- **MEMORY_AND_DISK**: RDD saved as deserialized Java object in JVM. If RDD doesn't fit in memory, some partitions stored on disk.
- **MEMORY_ONLY_SER**: RDD saved as serialized Java object (one byte array per partition). More space-efficient. Overflow partitions not cached.
- **MEMORY_AND_DISK_SER**: RDD saved as serialized Java object (one byte array per partition). More space-efficient. Overflow partitions saved on disk.
- **DISK_ONLY**: All RDD partitions saved on disk.

# Spark application