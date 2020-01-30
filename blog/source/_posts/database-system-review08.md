---
title: Database System Review 08
date: 2017-11-07 14:17:03
tags: database
categories: STUDY
---

## <center> Parallel Execution </center>

<!--more-->

### Parallel vs. Distributed

- Parallel DBMSs:
	- Nodes are physically close to each other.
	- Nodes connected with high-speed LAN.
	- Communication cost is assumed to be small
- Distributed DBMSs:
	- Nodes can be far from each other
	- Nodes connected using public network
	- Communication cost and problems cannot be ignored.

### Inter- vs. Intra-Query Parallelism

- Inter-Query: different queries are executed concurrently. Increases throughput and reduces latency.
- Intra-Query: execute the operations of a single query in parallel. Decreases latency for long-running queries.

### Process Model

A DBMS's <u>process model</u> defines how the system is architected to support concurrent requests from a multi-user application.

A <u>worker</u> is the DBMS component that is responsible form executing tacks on behalf of the client and returning the results.

- Approach #1: Process per DBMSs worker
- Approach #2: Process Pool
- Approach #3: Thread per DBMS worker

#### Process per DBMSs worker
Each worker is a seperate OS process.

- Relies on OS scheduler
- Use shared-memory for global data structures
- A process crash doesn't take down entire system

#### Process Pool
A worker use any process that is free in a pool.

- Relies on OS scheduler
- Use shared-memory for global data structures
- Bad for CPU cache locality

#### Thread per worker
Single process with multiple worker thread.

- DBMS has to manage its own scheduling
- May or may not use a dispatcher thread
- Thread crash (may) kill the entire system.

Advantages:

- Less overhead per context switch
- Don't have to manage shared memory

Thread per worker process model is not equal to intra-query parallelism

### Inter-Query Parallelism
Procide the illusion of isolation through <u>concurrency control</u> scheme.

### Intra-Query Parallelism
Execute operators in parallel, improve performance of a single query.

- Approach #1: Intra-Operator: 

	Operators are decomposed into independent instances that perform the same function on different subsets of data.
- Approach #2: Inter-Operator(pipeline parallelism)

	Operations are overlapped in order to pipeline data from one stage to the next without materialization.

### I/O Parallelism
Split the DBMS installation across multiple storage devides.

- Multiple Disks per Database
- One Database per Disk
- One Relation per Disk
- Split Relation across Multiple Disk

#### Multi-disk parallelism
Configure OS/hardware to staore the DBMS's files across multiple storage devices.

- Storage Appliances
- RAID Configuration

#### Database Partitioning
Some DBMSs allow you to specify the disk location of each individual database. Using buffer pool manager maps a page to a disk location.

#### Partitioning 
Split single logical table into disjoint physical segments that are stored/managed separated.

##### Vertical Partitioning 
Store a table's attributes in a seperate location. Have to store tuple info to recontruct the original record.

##### Horizontal Partitioning 
Divide the tuples of a table up into disjoint segments based on some partitioning key.

- Hash Partitioning 
- Range Partitioning 
- Predicate Partitioning 
