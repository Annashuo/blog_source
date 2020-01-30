---
title: Database System Review 12
date: 2017-11-24 10:03:44
tags: database
categories: STUDY
---
##<center> Timestamp Ordering </center>

<!--more-->
### Concurrency Control Approaches
- Two-Phase Locking(2PL): Determine serializability order of conflicting operations at runtime while txn execute.
- Timestamp Ordering(T/O): Determine serializability order of txns before they execute.

### T/O concurrency control
Use timestamps to determine the serializability order of txns. If <font style="color:red;">$TS(T_{i})<TS(T_{j})$</font>, then the DBMS must ensure taht the execution schedule is equivalent to a serial schedule where <font style="color:red;">$T_{i}$</font> appears before <font style="color:red;">$T_{j}$</font>.

Eache txn <font style="color:red;">$T_{i}$</font> is assigned a unique fixed timestamp that is monotonically increasing.

#### Recoverable Schedules
A schedule is <u>**recoverable**</u> if txns commit only after all txns whose changes they read, commit. Otherwise, the DBMS cannot guarantee that txns read data that will be restarted after recovering from a crash.

#### Basic Timestamp Ordering
Txns read and write objects without locks. Every object X is tagged with timestamp of the last txm that successfully did read/write:

- <font style="color:red;">$W-TS(X)$</font>: Write timestamp on X
- <font style="color:red;">$R-TS(X)$</font>: Read timestamp on X

Check timestamps for every operation. If txn tries to access an object "from the future", it aborts and restarts.

Generates a schedule that is conflict serializable if you do **not** use the Thomas Write Rule.

- No deadlocks because no txn ever waits
- Possibility of starvation for long txns if short txns keep causing conflicts.
- Permits schedules that are not **recoverable**

##### Basic T/O - READS
If <font style="color:red;">$TS(T_{i})<W-TS(X)$</font>, this violates timestamp order of <font style="color:red;">$T_{i}$</font> with regard to the writer of <font style="color:red;">$X$</font>. Abort <font style="color:red;">$T_{i}$</font> and restart it with same TS. Else allow <font style="color:red;">$T_{i}$</font> to read X. Update <font style="color:red;">$R-TS(X)$</font> to max(<font style="color:red;">$R-TS(X),\ TS(T_{i})$</font>). Have to make a locol copy of X to ensure repeatable reads for <font style="color:red;">$T_{i}$</font>.

##### Basic T/O - WRITES
If <font style="color:red;">$TS(T_{i})<R-TS(X)$</font> or <font style="color:red;">$TS(T_{i})<W-TS(X)$</font>, abort and restart <font style="color:red;">$T_{i}$</font>, else allow <font style="color:red;">$T_{i}$</font> to write <font style="color:red;">$X$</font> and update <font style="color:red;">$W-TS(X)$</font>, also have to make a local copy of <font style="color:red;">$X$</font> to ensure repeatable reads for <font style="color:red;">$T_{i}$</font>.

##### Thomas Write Rule
- If <font style="color:red;">$TS(T_{i})<R-TS(X)$</font>: abort and restart <font style="color:red;">$T_{i}$</font>
- If <font style="color:red;">$TS(T_{i})<W-TS(X)$</font>: <u>**Thomas Write Rule**</u>: ignore the write and allow the txn to continue. This violates timestamp order of <font style="color:red;">$T_{i}$</font>.
- Else, allow <font style="color:red;">$T_{i}$</font> to wrie <font style="color:red;">$X$</font> and update <font style="color:red;">$W-TS(X)$</font>

##### Basic T/O - PERFORMANCE ISSUES
- High overhead from copying data to txn's workspace and from updating timestamps.
- Long running txns can get starved.
- Suffers from timestamps bottleneck.

#### Optimistic Concurrency Control
The DBMS creates a private workspace for each txn. All modifications are applied to workspace. Any object read is copied into workspace.

When a txn commits, the DBMS compares its workspace write set to see whether it conflicts with other txns.

If there are no conflicts, the write set is installed into the "global" database.

##### OCC Phase
- **Read Phase**: track the read/write sets of txns and start their writes in a private workspace
- **Validation Phase**: when a txn commits, check whether it conflicts with other txns
- **Write Phase**: if validation succeeds, apply private changes to database. Otherwise abort and restart the txn.

##### OCC -  PERFORMANCE ISSUES
- High overhead for copying data locally
- Validation/Write phase bottlenecks
- Aborts are more wasteful because they only occur after a txn has already executed.
- Suffers from timestamp allocation bottleneck
- When a txn commits, all previous T/O schemes check to see whether there is a conflict with concurrent txns. This requires latches. If there are a lot of concurrent txns, then this is slow even if the conflict rate is slow.

#### Partition-Based T/O
Split the datebase up in disjoint subsets called <u>**partitions**</u>. Only check for conflicts between txns that are running in the same partition.

Txns are assigned timestamps based on when they arrive at the DBMS.

Partitions are protected by a single lock:

- Each tan is queued at the partitions it needs
- The txn acquires a partition's lock if it has the lowest timestamp in that partition's queue

##### Partition-Based T/O - PERFORMANCE ISSUES
Partition-Based T/O is fast if:

- The DBMS knows what partitions the txn needs before it starts
- Most txns only need to access a single partition

