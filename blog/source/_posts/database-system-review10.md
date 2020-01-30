---
title: Database System Review 10
date: 2017-11-08 15:09:29
tags: database
categories: STUDY
---
##<center> Concurrency Control Theory</center>

<!--more-->

### Transactions
A <u>transaction</u> is the execution of a sequence of one or more operations on a shared database to perform some higher-level function.

#### Strawman System(SQLite)
One and only one txn can be running at the same time in the DBMS. Before a txn starts, copy the entire database to a new file and make all changes to that file.

#### Formal Definitions
Database: A fixed set of named data objects(A, B, C,...)

Transaction: A sequence of read and write operations(R(A), W(B),...)

In SQL:

```
BEGIN
...
COMMIT/ABORT
```
#### Atomicity
Execute all its actions or executes no action at all.

- Approach #1: Logging

	DBMS Logs all actions so that it can undo the actions of aborted txns.
	
- Shadow Paging

	DBMS makes copies of pages and txns make changed to those copies. Only when the txn commits is the page made visible to others.

#### Consistency

- Database Consistency
- Transaction Consistency

#### Isolation
Users submit txns and each txn executes as if it was running by itself.

A <u>concurrency control</u> protocol is how the DBMS decides the proper interleaving of operations from multiple transactions.

Two main categories:

- Pessimistic: Don't let problems arise in the first place.
- Optimistic: Assume conflicts are rare, deal with them after they happen.

##### Formal Properties of Schedule

- **Serial Schedule**: A schedule that does not interleave the actions of different txns.
- **Equivalent Schedules**: The effect of executing the first schedule is identical to the effect of executing the second schedule.
- **Serializable Schedule**: A schedule that is equivalent to some serial execution of txns.

##### Conflicting Operations
Two operations conflict if:

- They are by different txns
- They are one the same object and at least one of them is a write.

Three types:

- R-W
- W-R
- W-W

##### Conflict Serializable Schedules
Conflict Serializable Schedules S is conflict equivalent to some serial schedule.

Ways to find out a schedule S is conflict serializable:

- if transform S into a serial schedule by swapping consecutive non-confliting operations of different txns.
- Dependency Graphs

	![](https://github.com/Annashuo/hello-world/blob/master/dependency_graphs.png?raw=true)
	
	- Operation $O_{i} \in T_{i}$
	- Operation $O_{j} \in T_{j}$
	- Operation $O_{i} $ and $ O_{j}$ conflicts
	- Operation $O_{i} $ early than $ O_{j}$ 

	Schedule S is conflict serializable if dependency graph has **no cycle**.
	
##### View Serializability
Schedules $S_{1}$ and $S_{2}$ are view equivalent:

- if $T_{1}$ reads initial value of A in $S_{1}$, then $T_{1}$ also reads initial value of A in $S_{2}$
- if $T_{1}$ reads value of A written by $T_{2}$ in $S_{1}$, then $T_{1}$ also reads value of A written by $T_{2}$ in $S_{2}$
- if $T_{1}$ writes final value of A in $S_{1}$, then $T_{1}$ also writes final value of A in $S_{2}$

#### Durability
All of the changes of committed txns ahsould be persistent.

### Uses <font style="color:red;">locks</font> to guarantee serializable

#### Lock Types

##### Locks and Latches
- Locks
	- Protects the index's logical contents from other txns.
	- Held for txn duration
	- Need to be able to rollback changes.

- Latches
	- Protects the critical sections of the index's internal data structure from other threads
	- Held for operation duration
	- Do not need to be able to rollback changes.

... | Locks | Latches
:--:|:--:|:--:
Seperate... | User txns | Threads
Protect... | Database contents | In-Memory Data Structure
During... | Entire txns | Critical sections
Modes... | Shared, Exclusive, Update, Intention | Read, Write
Deadlock | Detection & Resolution | Avoidance
...by... | Waits-for, Timeout, Aborts | Coding Discipline
Kept in... | Lock Manager | Protected Data Structure

##### Basic Lock Types

- S-LOCK: Shared locks for reads
- X-LOCK: Exclusive locks for writes

Compatibility Matrix

 | Shared | Exclusive
:--:|:--:|:--:
Shared | yes | no
Exclusive | no | no

#### Two-Phase Locking
Two-Phase Locking(2PL) is a concurrency control protocol that determines whether a txn is allowed to access an object in the database on the fly.

- Phase 1: Growing
	- Each txn requests the locks that it needs from the DBMS's lock manager
	- The lock manager grants/denies lock requests
- Phase 2: Shrinking
	- The txn is allowed to only release locks that it previously acquired. It cannot acquire new locks.

**Two-Phase Locking on its own is sufficient to guarantee conflic serializability. But it is subject to cascading aborting.**

##### 2PL-Cascading Aborts
One aborted txn may cause other txn aborts.

##### Problems about 2PL

- There are potential schedules that are serializable but would not be allowed by 2PL. Locking limits concurrency.
- May still have "dirty read"->Solution: **Strict 2PL**
- May lead to deadlocks->Solutions: **Detection** of **Prevention**

#### Strict Two-Phase Locking
A schedule is <u>strict</u> if a value written by a txn is not read or overwritten by other txns until that txn finishes.

Advantages:

- Does not incur cascading aborts
- Aborted txns can be undone by just restoring original values of modified tuples.

#### Deadlock Detection + Prevention
A <u>deadlock</u> is a cycle of transactions waiting for locks to be released by each other.

##### Deadlock Detection

**Wait-for** Graph: Nodes are txns, Edge fro $T_{i}$ to $T_{j}$ if $T_{i}$ is waiting for $T_{j}$ to release a lock. Check for cycles and make a decision to break it.
	
**Deadlock Handling**:
	
- Victim Selection(by age, by progress, by the # of items already locked, by the # of txns that we have to rollback with it), Also consider the # of times a txn has been restarted in the past.
- Rollback Length: Completely or Minimally

##### Deadlock Prevention
Priority: older-> higher priority.
	
- **Wait-Die**(Old waits for Young): If $T_{i}$ has higher priority, $T_{i}$ waits for $T_{j}$, otherwise $T_{i}$ aborts
- **wound-wait**(Young waits for Old): If $T_{i}$ has higher priority, $T_{j}$ abort, otherwise $T_{i}$ waits.

**When a txn restarts, its new priority is original timestamp.**

#### Hierarchical Locking

##### Intention Locks
A <u>intention lock</u> allows a higher level node to be locked in shared or exclusive mode without having to check all descendent nodes.

- Intention-Shared(IS): Indicates explicit locking at a lower level with shared locks.
- Intention-Exclusive(IX): Indicates locking at lower level with excludive or shared locks.
- Shared+Intention-Exclusive(SIX): The subtree rooted by that node is locked explicitly in shared mode and explicit locking is being done at lower level with exclusive-mode locks. Like S and IX at the same time.

Compatibility Matrix:

... | IS | IX | S | SIX | X
:--:|:--:|:--:|:--:|:--:|:--
IS | y | y | y | y | n  
IX | y | y | n | n | n
S | y | n | y | n | n
SIX | y | n | n | n | n
X | n | n | n | n | n