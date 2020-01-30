---
title: Database System Review 14
date: 2017-11-24 20:45:24
tags: database
categories: STUDY
---
##<center> Logging and Recovery </center>

<!--more-->

### Crash Recovery
Recovery algorithms are techniques to ensure database consistency, transaction atomicity, and durability despite falitures

Recovery algorithms: 

- Actions during normal txn processing to ensure that the DBMS can recover from a failure
- Actions after a failure to recover the database to a state that ensures atomicity, consistency, and durability.

### Storage Types
- Volatile Storage
	- Data does not persist after power is cut
- Non-volatile Storage
	- Data persists after losing power
- Stable Storage
	- A non-existent form of non-volatile storage that survives all possible failures scenarios.

### Failure Classification
#### Transaction Failures
- Logical Errors: Txn cannot complete due to some internal error condition
- Internal State Errors: DBMS must terminate an active txn due ti an error condition

#### System Failures
- Software Failure: Problem with the DBMS implementation
- Hardware Failure: The computer hosting the DBMS crashes; Non-volatile storage contents are assumed to not be corrupted by system crash

#### Storage Media Failure
- Non-Repairable Hardware Failure: A head crash or similar disk failure destroys all or part of non-volatile storage; Destruction is assumed to be detectable
- No DBMS can recover from this

### Buffer Pool Policies
#### Steal Policy
Whether the DBMS allows an uncommitted txn to overwrite the most recent committed value of an object in non-volatile storage.

- steal: is allowed
- no-steal: is not allowed

#### Force Policy
Whether the DBMS ensures that all updates made by a txn are reflected on non-volatile storage before the txn is allowed to commit.

- force: is enforced
- no-force: is not enforced

#### No-Steal + Force
This approach is the easiest to implement:

- Never have to undo changes of an aborted txn because the changes were not written to disk
- Never have to redo changes of a committed txn because all the changes are guaranteed to be written to disk at commit time.

### Shadow Paging
Maintain two seperate copies of the database(**master**, **shadow**). Updates are only made in the shadow copy. When a txn commits, atomically switch the shadow to be come the new master.

Buffer Pool: NO-STEAL + FORCE

Organize the database pages in a tree structure where the root is a single disk page.

There are two copies of the tree, the master and shadow. The root points to the master copy. Updates are applied to the shadow copy.

#### Disadvantages
- Copying the entire page table is expensive
	- Use a page table structured like a B+tree
	- No need to copy entire tree, only need to copy paths in the tree that lead to updated leaf nodes
- Commit overhead is high:
	- Flush every updated page, page table and root.
	- Data gets fragmented
	- Need garbage collection

### Write-Ahead Log
Record the changes made to the database in a log file before the change is made.

- Assume that the log is on stable storage
- Log contains sufficient information to perform the necessary undo and redo actions to restore the database after a crash.

Buffer Pool: STEAL + NO-FORCE

All log records pertaining to an updated page are written to non-volatile storage before the page itself is allowed to be over-written in non-volatile storage.

Write a $<BEGIN>$ record to the log for each txn to mark its starting point. When a txn finishes, the DBMS will write a $<COMMIT>$ record on the log and make sure that all log records are flushed before it returns an acknowledgement to application.

#### Implementation
When to write log entries to disk?

- When the txn commits
- Can use group commit to batch multiple log flushes together to amortize overhead

When to write dirty records to disk?

- Every time the txn executes an update?
- Once when the txn commits?

### Checkpoints
Output onto stable storage all log records currently residing in main memory

Output to the disk all modified blocks

Write a $<CHECKPOINT>$ entry to the log and flush to stable storage.


### Logging Schemes
Physical Logging:

- 	Record the changes made to a specific location inthe database
-  Example: Position of a record in a page

Logical Logging:

- Record the high-level operations executed by txns
- Not necessarily restricted to single page
- Example: The UPDATE, DELETE and INSERT queries invoked by a txn

#### Physiological Logging
Hybrid approach where log records target a single page but do not specify data organization of the page.

### ARIES(Algorithms for Recovery and Isolation Exploiting Semantics)
- **Write-Ahead Logging(WAL)**: Any change is recorded in log on stable storage before the database change is written to disk. Has to be STEAL + NO-FORCE
- **Repeating History During Redo**: On restart, retrace actions and restore database to exact state before crash.
- **Logging Changes During Undo**: Record undo actions to log to ensure action is not repeated in the event of repeated failures

### Log Sequence Numbers
Each data page contains a <font style="color:red;">pageLSN</font>, that is the LSN of the most recent update to that page. 

System keeps track of <font style="color:red;">flushedLSN</font>, that is the max LSN flushed so far.

Before page i can be written to disk, we must flush log at least to the point where:
$pageLSN <= flushedLSN$

### Normal Commit & Aborted Operations
#### Transaction Commit
- Write <font style="color:red;">COMMIT</font> record to log.
- All log records up to txn's <font style="color:red;">COMMIT</font> record are flushed to disk
- When the commit succeeds, write a special <font style="color:red;">TXN-END</font> record to log.

#### Transaction Abort
<font style="color:red;">prevLSN</font>: the precious LSN for the txn.

- First write an <font style="color:red;">ABORT</font> record to log
- Play back updates in reverse order
- At end, write a <font style="color:red;">TXN-END</font> log record.

**Compensateion Log Records(CLR)**: describe the actions taken to undo the actions of a previous update record.

### Fuzzy Checkpointing
#### Non-fuzzy Checkpoint
The DBMS halts everything when it takes a checkpoint to ensure a consistent snapshot.

#### Better Checkpoints
Pause txns while the DBMS takes the checkpoint

#### Fuzzy Checkpoint
- <font style="color:red;">CHECKPOINT-BEGIN</font>
- <font style="color:red;">CHECKPOINT-END</font>

### Recovery Algorithm
#### ARIES - Recovery Phase
- Analysis: read the WAL to identify dirty pages in the buffer pool and active txns at the time of the crash.
- Redo: repeat all actions starting from an appropriate point in the log
- Undo: reverse the actions of txns that did not commit before the crash