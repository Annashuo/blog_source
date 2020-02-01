---
title: Database System Review 03
date: 2017-09-21 11:08:34
tags: database
categories: STUDY
---
Introduction to database storage.

<!--more-->
### Why not use OS?
If you rely on the operating system, then you have less choise in your design space inside the database system.

If using the operating system, when the process hit a page fault and it need to hold locks to some tuples or other latches to other data structures. It will be blocking for a long time and all other threads that are running other queries and other transactions could not be doing useful work but they can because all the locks.

### What DBMS can do for storage?

- Specialized prefetching
- Buffer replacement policy
- Thread/process scheduling
- Flushing data to disk

### File Storage(how pages stored in file)
Storage manager organized the files as a collection of pages.

- Tracks data read/written to pages
- Tracks the available space

#### Four types of page storage architecture
**Different DBMS manage pages in files on disk in different ways**

- Heap File Organization
- Sequential/ Sorted File Organization
- Hashing File Organization
- Log-Structured File Organization

#### HEAP file Organization
A heap file is an unordered collection of pages where tuples that are stored in random order.

Two ways to represent a heap file:

- Linked list
- Page directory

##### Linked list
Maintain a <u>header page</u> at the beginning of the file that stores two pointers:

- Head of the <u> free gage list</u>
- Head of the <u> data page list</u>

![](https://github.com/Annashuo/hello-world/blob/master/linked-list.png?raw=true)

##### Page directory
Maintain special pages that tracks the location of the data pages in the database files. The directory also records the number of free slot per page.

The DBMS has to make sure that the directory pages are in sync with the data pages.

![](https://github.com/Annashuo/hello-world/blob/master/page-directory.png?raw=true)

#### Log-Structured file organization
The DBMS only stores log recoreds instead of tuples in pages.

The system appends log records to the file of how the database was modified:

- Inserts store the entire tuple
- Deletes mark the tuple as deleted
- Updates contain the delta of just the attributes that were modified

To read a record, the DBMS scans the log backwards and "recreates" the tuple.

Build indexes to allow it to jump to locations in the log.

Periodically compact the log.

![](https://github.com/Annashuo/hello-world/blob/master/log-structured.png?raw=true)

### Page Layout(how tuples stored in page)

A <font color=red>page</font> is a fixed-size block of data

- It can contain tuples, meta-data, indexes, log records(database page can be 1KB~16KB, but hardware page and OS page are always 4KB)
- Most systems do not mix page types
- Some systems require a page to be self-contained.

Each page is given a unique identifier. The DBMS uses an indirection layer to map page ids to pysical locations.

#### Page header
Each page contains a <u>header</u> that records meta-data about the page's contents.

- Page size
- Checksum
- DBMS Version
- Transaction Visibility

#### Slotted Pages(most common layout)
The page maps "slots" to offsets. The length of tuples can be fixed or variable.

The header keeps track of:

- The # of used slots
- The offset of the starting location of the last slot used

![](https://github.com/Annashuo/hello-world/blob/master/slotted-page.png?raw=true)

### Tuple Layout
A tuple is essentially a sequence of bytes. DBMS interpret those bytes into attribute type and values.

Each tuple is assigned a unique record identifier

- Most common: $page\_id + offset/slot$
- Can also contain file location info

#### Tuple header
Header of each tuple contains meta-data:

- visibility(concurrency control)
- Bit map for NULL values

#### Data representation

- INTEGER/BIGINT/SMALLINT/TINYINT: C/C++ representation
- FLOAT/REAL vs. NUMERIC/DECIMAL: IEEE-754 Standard/ Fixed-point Decimals
- VARCHAR/VARBINARY/TEXT/BLOB: Header with length, followed by data bytes
- TIME/DATA/TIMESTAMP: 32/64-bit integer of seconds

### Locks vs. Latches

#### Locks

- Protects the database's logical contents from other transactions
- Held for transaction duration
- Need to be able to rollback changes

#### Latches:

- Protects the critical sections of the DBMS's internal data structure from other threads
- Held for operation duration
- Do not need to be able to rollback changes

### Buffer Pool
The <u>buffer pool</u> is an in-memory cache of pages read from disk.

#### Buffer pool organization
Memory region organized as an array of fixed-size pages.

An array entry is called a <font color=red>frame</font>

When DBMS requests a page, an exact copy is placed into one of these frames.

#### Buffer pool page table

The page table keeps track of pages that are currently in memory and also maintains additional meta-data per page.

meta-data:

- dirty flag
- pin counter

<u>Pin counter</u> do not equals to the <u>latches</u>. If DBMS want to modify a page, it should put a latch on that page. If DBMS want to add a new entry, it needs to put a latch on the page table.

![](https://github.com/Annashuo/hello-world/blob/master/buffer-pool.png?raw=true)

#### Multiple Buffer Pools
Helps reduce latch contention and improve locality.

- Multiple buffer pool instances
- Per-database buffer pool
- Per-page type buffer pool

#### Pre-fetching
1. Different query plan has different pre-fetching result.
	- sequential scans
	- index scans
2. scan sharing(queries reuse data retrieved from storage or operator computations)
	- queries do not have to be exactly the same
	- Can also share intermediate results.

#### Buffer replacement policies
##### Goals:

- correctness
- accuracy
- speed
- meta-data overhead

##### LRU
Maintain a timestamp of when each page was last accessed

Keep the pages in sorted order to reduce the search time on eviction

##### Clock
1. Approximation of LRU without needing a seperate timestamp per page
	- Each page has a reference bit
	- When a page is accessed, set to 1
2. Organize the pages in a circular buffer with a "clock hand":
	- Upon sweeping, check if a page's bit is set to 1
	- If yes, set to 0. If no, then evict.

#### Problems
Both LRU and CLOCK are susceptible to sequential flooding. Th most recently used page is actually the most unneeded page.

#### Better solutions

- LRU-K: take into account historyof lats K references
- Priority Hints: allow txns to tell the buffer pool whether a page is important or not.
- Localization: choose pages to evict on a per txn/query basis

#### Allocation policies
1. Global policies: make decisions for all active txns
2. Local policies:
	- allocate frams to a specific txn without considering the behavior of concurrent txns
	- still need to support sharing pages

### Other memory pools
Not for just tuples and indexes, not always backed by disk

- sorting + join buffers
- query caches
- maintenance buffers
- log buffers
- dictionary caches


