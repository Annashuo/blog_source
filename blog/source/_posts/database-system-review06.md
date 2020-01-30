---
title: Database System Review 06
date: 2017-10-10 13:30:02
tags: database
categories: STUDY
---
This post contains a couple of frequently used relational operators.

<!--more-->
### Sorting algorithms
#### WHY
1. In relational model, tuple in a table have no specific order
2. SELECT ... ORDER BY
	- Users often want to retrieve tuples in a specific order
	- Trivial to support duplicate elimination(DISTINCT)
	- Bulk loading sorted tuples into B+ tree index is faster

3. SELECT ... GROUP BY -> sort-merge join algorithm

#### Based on memory
If data fits in memory, use standard sorting algorithm like **quick sort**

If data does not fit in memory, use **external sorting**, **B+ TREE index**

#### External merge sort
Frequently used external sorting algorithm

Idea: Hybird **sort-marge** strategy

- Sorting phase: sort small chunks of data that in main-memory, and then write back the sorted data to a file on disk
- Merge phase: combine sorted sub-files into a single larger file

##### 2-way External Merge Sort
Assuming files are broken up into <font style="color:red">N</font> pages. The DBMS has a finite number of <font style="color:red">B</font> fixed-size buffers.

Pass 0: 

- Reads every B pages of the tables into memory
- Sort them and write them back to disk
- Each sorted set of pages is a <font style="color:red">run</font>

Pass 1,2,3,...:

- Recursively merges pairs of runs into runs twice as long
- Uses three buffer pages(two for input, one for output)

Number of passes = <font style="color:red">$1+\left \lceil log_{2}N \right \rceil$</font>

Total I/O cost = <font style="color:red">$2N * (\#\ of\ passes)$</font>

This algorithm only requeries 3 buffer pages, buffer pages utilization is pretty low.

##### General External Merge Sort(K-way external merge sort)
Pass 0: use <font style="color:red">B</font> buffer pages, produce <font style="color:red">$\left \lceil N/B \right \rceil$</font> sorted runs of size B

Pass 1,2,3,...: merge <font style="color:red">B-1</font> runs(K-way merge)

Number of passes = <font style="color:red">$1+\left \lceil log_{B-1}\left \lceil N/B \right \rceil \right \rceil$</font>

Total I/O cost = <font style="color:red">$2N * (\#\ of\ passes)$</font>

###### K-way merge algorithm
Input: K sorted sub-arrays 

Efficiently computes the **minimum element** of K sub-arrays

Repeatedly transfers that element to output array.

Internally maintains a heap to efficiently compute minimum element.

Time Complexity = <font style="color:red">$O(N\ log_{2}K)$</font>

#### Using B+ Tree
If table that must be sorted already has a B+ tree index on the sorted attribute(s)

Idea: Retrieve tuples in desired sorted order by simply traversing the leaf pages of the tree

Cases to consider:

- Clustered B+ tree: traverse to the left-most leaf page and then retrieve tuples from all leaf pages. **better than external sorting**
- Unclustered B+ tree: Chase each pointer to the page that contains the data. Generally one I/O per data record.**Bad!**

#### Alternatives to sorting
GROUP BY: may no need to be sorted

DISTINCT: use hashing to remove duplicates without ordering, computationally cheaper than sorting.

### JOIN algorithms
#### WHY
1. In relational model
	- unnecessary repetition of information must be avoided
	- decompose tables using normalization theory

2. SELECT ... JOIN
	- Reconstruct original tables via joins
	- No information loss

#### Join algorithms

- Nested Loop Join
	- Simple Nested Loop Join
	- Block Nested Loop Join
	- Index Nested Loop Join
- Sort-Merge Join
- Hash Join

#### Join operator output

- data: copy values for the attributes in outer and inner tuples into a new output tuple. subsequent operators in the query plan never need to go back to the base tables to get more data.
- record id: only copy the joins keys along with the record ids of the matching tuples. Ideal for column stores because the DBMS does not copy data that is not need for the query(also called **materialization**)

#### I/O cost algorithm
Assume

- <font style="color:red">M</font> pages in R, <font style="color:red">$p_{R}$</font> tuples per page, <font style="color:red">m</font> tuples total
- <font style="color:red">N</font> pages in S, <font style="color:red">$p_{S}$</font> tuples per page, <font style="color:red">n</font> tuples total

Ignore output cost: <font style="color:red">Cost metric = # of I/Os</font>

#### Nested Loop Join
##### Simple Nested Loop Join
2 buffers for steaming the tables and 1 for storing output

```
foreach tuple of R(outer table)
	foreach tuple of S(inner table)
		output, if r and s match
```

<font style="color:red">Cost = M + (m*N)</font>

##### Block Nested Loop Join
The small table (in terms of # of pages) should be outer.(in this example, M<N)

- if buffer = 3(2 for input, 1 for output),
 
	```
	read block from R
		read block from S
			output, if pair of tuples match
	```
	
	<font style="color:red">Cost = M + (M*N)</font>

- if buffer B < M + 2,

	Use <font style="color:red">B-2</font> buffers for scaning outer table, 1 buffer to scanning inner table, 1 for storing output

	```
	read B-2 blocks from R
		read block from S
			output, if a pair of tuples match
	```
	
	<font style="color:red">$Cost = M + (\left \lceil M/(B-2) \right \rceil *N)$</font>
	
- if buffer B >= M + 2

	<font style="color:red">Cost = M + N</font>
	
##### Index Nested Loop Join
Use an index to find **inner table matches**

```
foreach tuple r of R
	foreach tuple s of S, where ri = sj
		output, if ri and sj match
```
<font style="color:red">Cost = M + (m*C)</font> (C is index look-up cost)

##### Nested Loop Join SUMMARY

- Pick the small table as outer table
- Buffer as much of outer table in memory as possible
- Loop over the inner table or use an index

#### Sort-Merge Join

- Sort Phase: sort **both** tables on the join attribute
- Merge Phase: scan the two sorted tables in parallel and emit matching tuples.

Useful when one or both tables are already sorted or output must be sorted on join key

<font style="color:red">Cost = [(2M\*logM/logB) + (2N*logN/logB)] + [M+N]</font>

#### Hash Join

Partitioning Phase:Read and Write both tables. <font style="color:red">Cost = 2(M+N)</font>

Probing Phase:Read tables. <font style="color:red">Cost = (M+N)</font>

Total: <font style="color:red">Cost = 3(M+N)</font>

##### Basic hash join algorithm

- Phase #1: Build

	Scan the outer relation and populate a hash table using hash function $h_{1}$ on the join attributes
	
- Phase #2: Probe

	Scan the inner relation and use $h_{1}$ on each tuple to jump to a location in the hash table and find a matching tuple
	

build hash table H for R</br>
foreach tuple s of S</br>
	output, if $H_{1}(s_{j}) \in HT(R)$ 

##### Hash table values

- Approach 1: Full tuple
	- Avoid having to retrieve the outer relation's tuple contents on a match
	- Takes up more space in memory

- Approach 2: Tuple identifier
	- Ideal for column stores because the DBMS doesn't fetch no need data from disk 
	- Better if join selectivity is slow

##### Grace Hash Join
When no enough memory to fit the entire hash table

- Phase #1: Build

	Hash both tables on the join attribute into partitions
	
- Phase #2: Probe

	Compares tuples in corresponding partitions for each table
	
If buckets don't fit in memory, use **recursive partitioning**, build another hash table for buckets using hash function $h_{2}$($h_{1}\not= h_{2}$)

#### JOIN SUMMARY

Join algorithm | I/O cost 
:--:|:--:|
Simple Nested Loop Join | M+(m\*N)
Block Nested Loop Join | M+(M\*N)
Index Nested Loop Join | M+(m*logN)
Sort Merge Join | M+N+(sort cost)
Hash Join | 3(M+N)