# Distributed databases
Simply a distributed system that allows read/write access to data.

Three properties
- replication: keep a copy of the same data on several different nodes
- partitioning: split the database into smaller subsets and distribute the partitions to different nodes
- transactions: units of work that group several reads and writes to be performed together in the database

## Replication
Replication allows the following
- data scalability: allow more machines to serve read-only requests, increases read throughput
- geo scalability: data geographically close to clients
- fault tolerance: if a part of the system goes down, the entire system should still work

Steps for replication:
1. Take a consistent snapshot from the leader
2. Ship it to the replicas
3. Get an id of the leader’s replication log state at the time of snapshot creation
4. Initialize the replication function to the latest leader id
5. The replica must retrieve and apply the replication log until it catches up with the leader

Replication example:
```
> SHOW MASTER STATUS;
+--------------------+----------+
| File               | Position |
+--------------------+----------+
| mariadb-bin.004252 | 30591477 |

>CHANGE MASTER TO
  MASTER_HOST='10.0.0.7',
  MASTER_USER='replicator',
  MASTER_PORT=3306,
  MASTER_CONNECT_RETRY=20,
  MASTER_LOG_FILE='mariadb-bin.452',
  MASTER_LOG_POS= 30591477;
```

Replication result:
```
> SHOW SLAVE STATUS\G

Master_Host: 10.0.0.7
Master_User: replicator
Master_Port: 3306
Master_Log_File: mariadb-bin.452
Read_Master_Log_Pos: 34791477
Relay_Log_File: relay-bin.000032
Relay_Log_Pos: 1332
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
```

### Replication log implementation
When logs are to be replicated, different ways to replicate them:
- **statement based**: replicate by copying the exact write statement of the master/leader
- **write-ahead log based**: before any actual replication, the followers write the intended change to an append-only **write-ahead log** or **WAL**
- **logical based**: database generates a stream of logical updates for each update to the WAL

Statement-based replication is non-deterministic and almost never used. Because state of one node is never guaranteed to be the same for the state of another node. Below, the NOW() is different per node:
```
UPDATE foo
SET updated_at=NOW()
WHERE id = 10
```

WAL-based replication *writes all changes to the leader WAL*. The followers apply the WAL entries to get consistent data. Problematic as WAL is bound to the current data structure. If leader's data structure changes, followers stop working.

In logical-based replication the database generates a stream of **logical updates** for each update to the WAL. Logical updates can be:
- For new records, the values that were inserted
- For deleted records, their unique id
- For updated records, their id and the updated values

### Replication distribution
Replication distribution:
- synchronous: leader reports success when certain number of followers confirm writes were made on their disks 
- asynchronous: leader reports success immediately after write committeed to its own disk. followers apply changes on their own pace

#### Synchronous distribution
- A follower is guaranteed to have an up-to-date copy of the data that is consistent with the leader.
- If the leader suddenly fails, we can be sure that the data is still available on the follower.
- If the synchronous follower does not respond, the write cannot be processed.
- The leader must block all writes and wait until the synchronous replica is available again.

![Image](../../images/synchronous.PNG)

With sync, the current write must be completed before leader can start any other write replication. If another write operation in the queue, need to wait until certain number of followers are done replicating the current one; it’s *impractical for all followers to be synchronous*.

#### Asynchronous distribution
- Higher availability: The leader does not need to block writes in case of inaccessible follower.
- A follower is not guaranteed to have an up-to-date copy of the data that is consistent with the leader.
- Writes are not guaranteed to be durable in case of leader failure.

![Image](../../images/async.PNG)

For asyn if leader goes down, no way of knowing which nodes successed and which ones failed. In addition all nodes not guaranteed to have same consistent state since each process and finish time can be different and can have different states at a given point.

![Image](../../images/async_problem.PNG)

Async is hard to implement due to many possible problems:
- **Read-after-write**: Clients may not see their own writes, i.e., when they connect to a replica which does not have the update.
- **(non-) Monotonic reads**: When reading from multiple replicas concurrently, a stale replica might not return records that the client read from an up to date one.
- **Causality violations**: Updates might be visible in different orders at different replicas. This allows for violations of the happened-before property.

### Replication architecture
In a replicated system, there are two node roles:
- leader/master: node that accepts write queries from clients
- follower/slaves/replicas: nodes that provide *read-only access* to data

Several replication architecture depending on the role configurations:
- leader-based or master-slave: a single leader accepts writes and distributes the update to followers
- multi-leader: multiple leaders accept writes while keeping themselves in sync. Then distributes update to followers
- leaderless: All nodes are peers in the replication network

#### Multi-leader replication
Multiple leaders to accept writes. Replication to followers is similiar to single-leader case. Here **write conflicts** may arise, which is when multiples leaders concurrently update the same record:

![Image](../../images/write_conflicts.PNG)

Code example:
```
                                # Clock
# User 1
git clone git://....            # t+1
git add foo.c                   # t+2
git commit -a -m 'Hacked v1'    # t+3
git push                        # t+5

# User 2
git clone git://....            # t+2
git add foo.c                   # t+5
git commit -m 'Hacked new file' # t+6
git push # fails                # t+7
git pull # CONFLICT             # t+7
```

To avoid this, we can have *one leader per session*.

Or converge to consistent state by applying the one of the following conflict resolution policies:
- **last-write-wins** policy: order writes by timestamp (may lose data)
- **report conflict to application** policy: let the application resolve the conflicts(like Git or Google docs)
- **conflict-free data types** policy: use conflit-free data types with specific conflict resolution logics

#### Leaderless replication
No leader exists in this scenario -> any node (replica) can accept read/write queries from the clients.

Writes are successful if written to W replicas and reads are successful if written to R replicas.
For W, R and N replicas:
- if W + R > N : We expect to read up-to-date value
- if W < N : We can process writes if a node is unavailable
- if R < N : We can process reads if a node is unavailable

![Image](../../images/leaderless.PNG)

Replication of a total order of operations is fundamentally a consensus problem.

## CAP theorem
The **CAP theorem** states that in a replicated system it is impossible to get all three of:
- (Strong) Consistency: All nodes in the network have the same (most recent) value
- Availability: Every request to a non-failing replica receives a response
- Partition tolerance: The system continues to operate even if component or network faults

Synchronous replication: Strong consistency but not always available
Data not always available in case a failure of any replicas.

Asynchronous replication: Always available but not strong consistency
Replicas may not have up to date data.

*Trade-off is not “Availability vs Consistency” but “Availability vs Strong Consistency”*

## Consistency model
**Consistency model** is a contract between programmer and replicated system, i.e., it specifies the consistency between replicas and what can be observed as possible results of operations.

It answers questions such as:
- What are the possible results of a read query?
- Can a client see its own updates?
- Is the causal order of queries preserved at all copies of data?
- Is there a total order of operations preserved at all replicas?

![Image](../../images/consistency_question.PNG)

![Image](../../images/consistency_model.PNG)

**Strong consistency** means maintaining the illusion of a single copy. These models trade off availability for strong consistency. Focuses on *total order*

Two main types of weak consistency models:
- Sequential consistency
- Linearizable consisntency

**Weak consistency models** trade-off strong consistency for better performance. Focuses on *partial order*

Three main types of weak consistency models:
- Eventual consistency
- Causal consistency
- Client-centric consistency models (Read your writes, monotonic reads, monotonic writes)

### Strong consistency: Sequential consistency
Here the operations take place in some *total order* that is consistent with the order of operations for ALL replicas. This doesn't consider the true order of operations that happen in real time. Therefore the following two examples are sequentially consistent:

![Image](../../images/sequential_consistent.PNG)

But the following is not sequentially consistent as the orders of R3 and R4 are different.

![Image](../../images/causally_consistent.PNG)

### Strong consistency: Linearizable consistency
This combines sequential consistency + the total order of operations conform to the real time ordering:

![Image](../../images/sequential_linearizable.PNG)

The following is linearlizable:

![Image](../../images/linearlizable.PNG)

The following is NOT lienarlizable as it doesn't show the true order of real time:

![Image](../../images/not_linearizable.PNG)

![Image](../../images/not_linearizable1.PNG)

It has the following property:
- As soon as writes complete successfully, the result is immediately replicated to all nodes atomically and is made available to reads.
- At any time, concurrent reads from any node return the same values.

### Weak consistency: Eventual consistency
All updates eventually delivered to all replicas.

All replicas reach consistent steate once no more user updates arrive.

Examples:
- Search engines: Search results are not always consistent with the current state of the web
- Cloud file systems: File contents may be out-of-sync with their latest versions
- Social media applications: Number of likes for a video may not be up to date

### Weak consistency: Causal consistency
Causally related operations deliverd to other replicas in correct order; maintains a *partial order* of events based on causality.

The following is allowed:

![Image](../../images/casual_consistency.PNG)

The following is NOT allowed as it breaks partial order:

![Image](../../images/casual_consistency2.PNG)

Ideal for messaging applications.

### Weak consistency: Client-centric consistency
Provide guarantees about ordering of operations *only for a single client process*:

- **Monotonic reads**: If a process reads the value of x, any successive read operation on x by that process will always return that same value or a more recent value.
- **Monotonic writes**: If a process writes a value to x, the replica on which a successive operation is performed reflects the effect of a previous write operation by the same process
- **Read your writes**: The effect of a write operation by a process on x will always be seen by a successive read operation on x by the same process
- **Writes follow reads**: If a process writes a value to x following a previous read operation on x by the same process, it is guaranteed to take place on the same or more recent values of x that was read.

## Partitioning
Partitioning breaks the whole dataset into different parts and distributes them to different hosts. Also known as **sharding**. 

Partition allows queries to be run in parallel on parts of the dataset, and read/write are spread on multiple machines.

Three types of partitioning:
- **Range partitioning**: Takes into account the natural order of keys to split the dataset in the required number of partitions. Requires keys to be naturally ordered and keys to be equally distributed across the value range.
- **Hash partitioning**: Calculates a hash over the each item key and then produces the modulo of this hash to determine the new partition.
- **Custom partitioning** Exploits locality or uniqueness properties of the data to calculate the appropriate partition to store the data to. An example would be pre-hashed data (e.g. git commits) or location specific data (e.g. all records from Europe).

![Image](../../images/partition_types.PNG)

On partitioned datasets, clients need to be aware of the partitioning scheme in order to direct queries and writes to the appropriate nodes. But most partitioned systems feature a **query router** component that sits between client and the partitions. The query router knows the employed partitioning scheme and directs requests to the appropriate partitions.

Partitioning is always combined with replication as a node failure will result in irreversible data corruption. As shown below, each shard has the primary dataset part and secondary (replicated) dataset parts:

![Image](../../images/partition_example.PNG)

## Transactions
(read up on at night)
### Isolation vs Consistency


# Distributed filesystems
**Filesystem** is a process that manages how and where data on a storage disk is stored, accessed, and managed. Different from **database** which is a organized physical collection of data.

![Image](../../images/database_vs_filesystem.PNG)

Primary job of the filesystem is to make sure that the data is always accessible and in tact. To maintain consistency, most modern filesystems use a log (just like databases).

Windows and MaxOSX already have network filesystems. But additional distributed filesystems are needed as big data processing arose:
- Data availability across different locations
- Existing file systems like CIFS NFS, are not distributed, as there is a centralized server to maintain consistency. This results in bottleneck for huge datasets

Some notable filesystems:
- Google File System (GFS)
- Hadoop Distributed File System (HDFS)
- CloudStore
- Amazon Simple Storage Service (Amazon S3)

## Google filesystem
**Google filesystem** is first notable distributed file system. It is able to:
- Process large sized files and large number of files
- Two main reads: large streaming reads and rare small random reads
- Process data in bulk at high rate
- Fault tolerant
- High availability

Advantages:
Reduces interaction w/ master
Reduces metadata stored on master

Disadvantages: 
Small files may become hotspots
-> single node maintains all of the metadata such as namespace, ACLs, mapping from files to chunks, and current location of chunks.

### GFS architecture
The GFS architecture has:
- single master (but can also be replicated)
- multiple chunkserers
- multiple clients can connect to GFS

![Image](../../images/gfs.PNG)

GFS is a **journaled filesystem**, meaning all operations are *added to a log first, then applied*. Checkpoints on the log are created periodically and log is replicated across nodes.

### GFS storage model:
- single file can contain many objects (web documents)
- Files are divided into fixed size chunks (64MB) with unique identifers generated at insertion time
- Disk seek time small compared to transfer time
- Single file larger than node's disk space
- fixed size makes allocation computations easy
- Files are replicated across chunk servers (at least 3, so can be in 1 chunk server of 3 instances or 1 instance for 3 different chunk server)
- Neither client nor chunkserver caches file data (since data is huge)

### GFS operations
GFS read:
1. Client sends read request to master server
2. Master replies with location and identifier of the chunkserver replicas
3. Client caches the metadata
4. Client sends the data to one of the chunkserver replicas
5. The corresponding chunkserver replica returns the requested data

*notice how master replies with location of multiple replicas but client only sends to one of them*

GFS write:
1. Client sends a write request to master server
2. Master replies with location of the *chunkserver replicas and the primary replica*
3. Client sends the data to ALL of the chunkserver replicas' buffers
4. Once the replicas receive the data, the client tells the primary replica to begin the write function (primary assigns serial number to write requests)
5. Primary replica writes the data to the appropriate chunk and then the same is done on the secondary replicas
6. Secondary replias complte the write function and reports back to primary
7. primary sends confimation to client

*notice how master replies with location of multiple replicas and client sends data to ALL of them*
*eventual consistency. If one of the replicas fail, it will come back alive in stale state and retry to be consistent*

![Image](../../images/gfs_write.PNG)

GFS operation:
- master does not keep a persistent record of chunk locations but instead queries the chunkservers at statup and then updates it by periodic polling.
- If current master fails, recovers by rerunning the operation log.
- If chunkserver fails, it just restarts.
- Chunkservers use checksums to detect data corruption.
- master maintains a chunk version number to distinguish between up-to-date and stale replicas.
- before operation on a chunkserver, master ensures version number is up-to-date.

### GFS consistency model
GFS has a *relaxed consistency model* (**eventual consistency**) that favors availability over strong consistency. This means:
- File namespace mutations (file creation) are atomic, handled in master (global order)
- state of a file region after a data mutation depends on the type of mutation, whether it succeeds or fails, and whether there are concurrent mutations. 

Terms to know to understand GFS:
- **Consistent**: all replicas have the same value -> all clients see always the same data regardless of which replica they read from
- **Defined**: each replica reflects the performed mutation
- **Write**: erase what was previously on the file
- **Append**: add new information to the end of the file 

append completes atomically *at least once*. Remember in distributed systems no guarantee of exactly once.

![Image](../../images/gfs_consistency_model.PNG)

*Scenario 1: failed concurrent record append that could result in undefined and inconsistent regions*
If a replica failed to acknowledge the mutation, it may not have performed it. In that case when the client retries the append, this replica will have to add padding in place of the missing data, so that the record can be written at the right offset. So one replica will have padding while other will have the previously written record in this region.

*Scenario 2: successful concurrent record append that could result in record duplication, defined but inconsistent*
If a record append fails at any replica, the client retries the operation. As a result, replicas of the same chunk may contain different data possibly including duplicates of the same record in whole or in part.
-> any failure on a replica (e.g. timeout) will cause a duplicate record on the other replicas. This can happen without concurrent writes.

*Scenario 3: failed concurrent writes that could result in undefined and inconsistency*
A failed write can cause an inconsistent region. 

*Scenario 4: successful concurrent writes that could result in consistent but undefined*
More interestingly, successful concurrent writes can cause consistent but undefined regions. If a write by the application is large or straddles a chunk boundary, GFS client code breaks it down into multiple write operations. They may be interleaved with and overwritten by concurrent operations from other clients. Therefore, the shared file region may end up containing fragments from different clients, although the replicas will be identical because the individual operations are completed successfully in the same order on all replicas. This leaves the file region in consistent but undefined state.

## HDFS - Hadoop Distributed File System
HDFS is similar to GFS but some differences:
- HDFS is a user-space filesystem written in Java.
- HDFS is opensource. 
- Same architecture as GFS but different names.

![Image](../../images/hadoop.PNG)

HDFS looks like a UNIX filesystem, but does not offer the full set of operations.

![Image](../../images/hdfs.PNG)

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
