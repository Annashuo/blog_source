---
title: Database System Review 13
date: 2017-11-24 15:20:55
tags: database
categories: STUDY
---
##<center> Multi-Version Concurrency Control(MVCC) </center>

The DBMS maintains multiple <u>**physical**</u> versions of a single <u>**logical**</u> onject in the database:

- When a txn writes to an object, the DBMS creates a new version of that object
- When a txn reads an object, it reads the newest version that existed when the txn started.

**Writers don't block readers.**

**Readers don't block writers.**

Read-only txns can read a conststent <u>snapshot</u> without acquiring locks. Use timestamps to determine visibility.

Easily support <u>time-travlel queries</u>

<!--more-->
### Version Storage
Then DBMS uses the tuples' pointer field to create a version chain per logical tuple.

- Approach 1: Append-Only Storage
- Approach 2: Time-Travel Storage
- Approach 3: Delta Storage

#### Append-Only Storage
Version Chain Ordering:

- Approach 1: Oldest-to-Newest(O2N)
- Approach 2: Newest-to-Oldest(N2O)

#### Time-Travel Storage
On every update, copy the current version to the time-travel table. Update poiters. Overwrite master version in the main table. Update pointers.

#### Delta Storage
On every update, copy only the values that were modified to the delta storage and overwrite the master version.

### Garbage Collection
The DBMS needs to remove <u>reclaimable</u> physical versions from the database over time.

- Approach 1: Tuple-level
	- Find old versions by examining tuples directly
	- Background Vacuuming vs. Cooperative Cleaning
- Approach 2: Transaction-level
	- Txns keep track of their old versions so the DBMS does not have to scan tuples to determine visibility.

#### Tuple-level GC
- **Background Vacuuming**: Seperate thread(s) periodically scan the table and look for reclaimable versions. Works with any storage.
- **Cooperative Cleaning**: Worker threads identify reclaimable versions as they traverse version chain. Only works with **O2N**

#### Transaction-level GC
Each txn keeps track of its read/write set.

The DBMS determines when all versions created by a finished txn are no longer visible.

### Index Management
PKey index always point to version chain head.

Secondary indexes are moew complicated.

#### Secondary Indexes
- Approach 1: Logical Pointers
	- Use a fixed identifier per tuple that does not change.
	- Requires an extra indirection layer.
	- Primary Key vs. Tuple Id
- Approach 2: Physical Pointers
	- Use the physical address to ther version chain head
