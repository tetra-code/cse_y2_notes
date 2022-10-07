# Distrubted file system
Here we will use spark

Windows and MaxOSX already have network filesystems. But additional distriuted file systems are needed as big data processign arose because

- Availability across different locations
- Existing file systems like CIFS NFS, are not distributed, as there is a centralized server to maintain consistency, therefore could create bottleneck as data size became bigger.

**Google filesystem** first notable distributed file system. It is able to:

- Process large sized files and large number of files
- Two main reads: large streaming reads and rare small random reads
- Process data in bulk at high rate
- Fault tolerant
- High availability

The GFS architecture has

- single master (but can also be replicated)
- multiple chunkserers
- multiple clients can connect to GFS

The GFS storage model
- single file can contain many objects (web documents)
- Files are divided into fixed size chunks (64MB) with unique identifers generated at insertion time
- Single file larger than node's disk space
- fixed size makes allocation computations easy
- Files are replicated across chunk servers (at least 3, so can be in 1 chunk server of 3 instances or 1 instance for 3 different chunk server)
- Neither client nor chunkserver cache file data as the data is huge.

GFS write:
1. Client sends a request to master server
2. MASTER SENDS TO CLIENT THE LOCATION O THE CHUNKSERVER replica and primary replica
3. Client sends the write data to all replicas chunk server's buffer
4. Once replicas receive dat, client teslls primary replica to begin the write function (primary assigns serial number to write requests)
5. The primary replica writes the data to the appropriate chunk and same is done on secondary replicas
6. The secondary replias complte the write function and reports back to primary
7. primary sends confimation to client
(eventual consistency. If one of the replicas fail, it will come back alive in stale state and retry to be consistent)

GFS operation
- master does not keep a persistent reor of chunk locations but instead queries the chunk servers at statup and then updated by periodic polling.
- GFS is a journaled file
- If current master fails, recovers by rerunning the operation log
- If chunkserver fails, it just restarts

GFS consistency model
- Has a *relaxed consistency model* (eventual consistency) that supports highly distributed applications. 
- File namespace mutations are atomic (handled in master), handled in a global order
- State of file region (kept in chunkserver), whether it is concurrent mutaion or single one

Terms used specifically for GFS:
- Write: changes to data are orderesd as chosen by a primary
- Record append: completes atomically at least once
- Consistent: if all clients see always the same data, regardless of which replicas they read from
- Defined: if the region is consistent and clients will see what the mutation writes in its entirety

defined but inconsistent (writes guaranteed to be happen at least once)

GFS favors availability over what 
learn what happens in what mutation and such

# HDFS - Hadoop Distributed File System
Unlike GFS it is opensource. Same architecture as GFS but different names

Big data processing in distributed setting. It is expensive to move data from one location to other

- Latency. For accessing data in memory, it is 1000 slower on disk and in network 1000000 slower
- Partial failure. 100s of machines may fail at any time


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