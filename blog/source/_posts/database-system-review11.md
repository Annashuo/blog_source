---
title: Database System Review 11
date: 2017-11-09 14:45:36
tags: database
categories: STUDY
---
##<center> Index Concurrency Control</center>

<!--more-->

### Isolation Level

Isolation High -> Low

- serializable: no phantoms, all reads repeatable, no dirty reads; Obtain all locks first + index locks + strict 2PL
- repeatable reads: phantoms may happen; Obtain all locks first + strict 2PL
- read committed: phantoms and unrepeatable reads may happen; Obtain all locks first + S locks released immediately
- read uncommitted: all of them may happen; Obtain all locks first + allows dirty reads(no S locks)

... | Dirty Read | Unrepeatable Read | Phantom
:--:|:--:|:--:|:--:
serializable | no | no | no
repeatable read | no | no | maybe
read committed | no | maybe | maybe
read uncommitted | maybe | maybe | maybe

### Lock Crabbing
Protocol to allow multiple threads to access/modify B+Tree at the same time.

- get lock for parent
- get lock for child
- release lock for parent if "safe"

A **safe node** is one that will not splict or merge when updated. Not full on insertion and more than half-full on deletion.

#### Search
Start at root and go down; repeatedly, acquire S lock on child then unlock parent.

#### Insert/Delete
Start at root and go down, obtaining X lock as needed. Once child is locked, check if it is safe. If child is safe, release all locks on ancestors.

### Bayer Schkolnick algorithm
Assume that the leaf is safe, and use S-locks & crabbing to reach it, and verify. If leaf is not safe, then do previous algorithm.

#### Search
Same as before.

#### Insert/Delete:
Set locks as if for search, get to leaf and set X lock on leaf. If leaf is not safe, release all locks and restart txn using previous Insert/Delete protocol.