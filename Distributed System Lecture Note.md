## Lecture1 Introduction

### Topics

#### Infrastructure

Behave distributed, looks not - Abstractions

#### Implementation

RPC, threads, concurrency.

#### Performance

Scalability: 2*computers -> 2\*throughput

#### Fault Tolerance

- Availability
- Recoverability - 
- Non-volatile storage
- Replication: should be independent.

#### Consistency

Put(k,v): save key and value using put.

Get(k): client send a key, server respond a value.

Don't consistent in different nodes sometimes if some function doesn't work.

- 弱一致性与强一致性

  Weak consistency scheme v.s. Strong consistency scheme.

  Communication is more important in strong consistency

### MapReduce

<The procedure of mapreduce>

## Lecture2 RPC and Threads

### Threads Related

#### I/O Concurrency

Start new UNIX process for request from each request.

#### Parallelism

N threads means N times faster.

#### Convenience

Background or not in main part of the program.

#### Asynchronous Program (Event Driven Programming)

Case: Server talk to different clients

### Thread Challenges

#### Lock

Sharing memory in different threads, the threads are racing to execute.

```
LD X, register
AD 1, register
ST register, X
```

- Solutions: Lock.

In Golang, mu.Lock(), mu.Unlock() make the data lock to specific status to make sure the correctness.

Go doesn't know any thing about the relationship between variables and locks.

#### Coordination

- Channels

- Synchronic condition

- Wait group

#### Deadlock

T1 is waiting T2, while T2 is waiting T1. $\rightarrow$ nothing happens

#### Case

Fetch all web pages - web crawler, and 

- prevent multiple fetches in parallel.
- figure out if all the pages are crawled.

Solution: DFS in a graph - remember which page is crawled

Use lock(), unlock() to make url fetch atomic 

## Lecture3 GFS

**Performance**: sharding

Fault: **fault tolerance** - replication

Tolerance, **replication**, inconsistency

**Consistency**: pay for low **performance**.

#### Problems

Bad replication design

#### GFS

##### Background

Goal: big and fast, global reusable, sharding, automatic recovery

Single data center, internal use, big file sequential read and write access.

Doesn't guarantee correct returned data, web search not so strict about the right data. 

##### Master Data

File name: array of chunk handles (non-volatile, nv)

Handle

- list of chunk servers (v) - master for reboot 
- version number - to disk (nv)
- primary (v)
- lease expiration (v)

LOG, CHECKPOINT - disk: chunk and version number

##### Read

- File name offset -> master

- Master sends chunk, list of servers, caches
- Chunk -> chunk server, server send back chunk data

##### Write

###### Case1: No Primary

- Find up-to-date replicas

The master to find out the most up-to-date chunk - according to **version number.**

- Pick a primary and others secondary servers
- Increments the version number
- Tells primary and secondary the version number, give the primary version a ***LEASE***

Master crash handling: when reboot, master tells the server and wait for if their P.S. version# is the latest.

Primary picks 

###### Case2: Primary

Primary picks offset of all replicas and told them to write at offset. Chunk is full or not.

Split brain - network partition: master talk to client but client cannot talk to master.

Primary lease expires and will simply stop executing client requests it ignore or reject client request after the lease expired and therefore if master cant talk to the primary, it has to wait for the lease expires.

###### Further implements

Primary detect the duplicate requests

Disk damaged

Not show data to readers