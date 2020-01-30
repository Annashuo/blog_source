---
title: Database System Review 04
date: 2017-09-26 09:56:26
tags: database
categories: STUDY
---
We are now going to talk about how to support the DBMS's execution engine to read/write data from pages.

Two types of data structures:

- Hash tables
- Trees

<!--more-->
### Data structures

- Internal meta-data
- Core data storage
- Temporary data structures
- Table indexes

#### Table index
A <u>table index</u> is a replica of a subset of a table's columns that are organized and/or sorted for efficient access using a subset of those columns.

Trade-off on the number of indexes to create per database

- Storage Overhead
- Maintenance Overhead

### Design decisions

- Data Organization: how to layout memory and what information to store inside of the data structure to support efficient access.
- Concurrency: how to enable multiple threads to access the data structure at the same time without causing problems.

### Hash Tables
A <u>hash table</u> implements an associative array abstract data type that maps keys to values.

#### Static hash table
Allocate a giant array that has one slot for <u>every</u> element that you need to record.

Requirement:

- know the number of elemts ahead of time
- each key is unique
- perfect hash function: $key1 \neq  key2 \Rightarrow hash(key1) \neq hash(key2)$ 

##### Chained hashing
Maintain a linked list of <u>buckets</u> for each slot in the hash table.

Resolve collisions by placing all elements with the same hash key into the same bucket

<font style="color:red">Insertion, deletion and lookup are all hash to its bucket and scan for it, depending on the bucket size.</font>

![](https://github.com/Annashuo/hello-world/blob/master/chained-hash.png?raw=true)

The hash table can grow infinitely because you just keep adding new buckets to the linked list.

When store a new entry or extend the linked list, just take a latch on the bucket.

###### Non-unique keys

- Choice #1: **Seperate Linked List**, store values in seperate storage area for each key.
- Choice #2: **Store in Bucket**, store dulicate keys entries in the same bucket.

![](https://github.com/Annashuo/hello-world/blob/master/non-unique-keys.png?raw=true)

##### Open-addressing Hashing
Single gaint table of slots.

Resolve collisions by linearly searching for the next free slot in the table.

<font style="color:red">Insertion, deletion and lookup are all hash to its bucket and scan for it, depending on the bucket size. But has to store the key in the index to know when to stop scanning.</font>

![](https://github.com/Annashuo/hello-world/blob/master/open-address-hash.png?raw=true)

Always requires a hasg table with ~2x the number of slots as the number of elements in order to avoid collisions of hashed keys.

##### Cuckoo Hashing
Use multiple hash tables with different hash functions 

<font style="color:red">Deletion and lookup are always O(1) because only one location per hash table is checked

On insert, check every table and pick anyone that has a free slot. If no table has a free slot, evict the element from one of them and then re-hash it find a new location</font>

Make sure that we don't get stuck in an infinite loop when moving keys.

If there is a circle, rebuild the entire hash tables with new hash functions

#### Dynamic Hash Tables
##### Extendible Hashing
Chained hashing approach when we split buckets instead of letting the linked list grow forever.

This requires reshuffling the entries on split, but the change is localized.

##### Linear Hashing
Maintain a <u>pointer</u> that tracks the next bucket to split.

When <u>any</u> bucket overflows, split the bucket at the pointer location.

### B-TREE family

- B-Tree
- B+Tree
- B$^{link}$-Tree
- B*Tree

#### B+Tree
A **B+Tree** is a self-balancing tree data structure that keeps data <font style="color: red;">sorted</font> and allows searches, sequential access, insertions and deletions in <font style="color: red;">O(log n)</font>

- Generalization of a binary search tree in that a node can have more than tw children
- Optimized for systems that read and write large blocks of data

##### B+Tree properties
A B+Tree is an M-way search tree with the following properties:

- perfectly balanced(every leaf node is at the same depth)
- Every inner node other than the root is at least half-full(M/2-1 <= #KEYS < M-1)
- Every inner node with k keys has k+1 non-null children

##### B+Tree nodes
Every node in the B+Tree contains an array of key/value pairs

- The keys will always be the column or columns that you built your index on
- The values will diff based on whether the node is classified as <u>**inner nodes**</u> or <u>**leaf nodes**</u>

##### B+Tree: leaf node values

- Approach #1 **Record ids**: a pointer to the location of the tuple that the index entry corresponds to.
- Approach #2 **Tuple Data**: the actual contents of the tuple is stored in the leaf node; secondary indexes have to store the record id as their values

##### B-Tree vs. B+Tree
B-Tree stored keys+values in all nodes on the tree. It's more space efficient since each key only appears once in the tree.

A B+Tree only stores values in leaf node. Inner nodes only guide the search process.

##### B+Tree: INSERT
```
Find correct lead L
Put data entry into L in sorted order.
{
	If L has enough space, done!
	Else must split L into L and a new node L2
	{	
		Redistribute entries evenly, a copy up middle key
		Inser index entry pointing to L2 into parent of L
	}
}
To split inner node, redistribute entries evenly but push up middle key.
```

##### B+Tree: DELETE
```
Start at root, find lead L where entry belongs
Remove the entry
{
	If L is at least half-full, done!
	If L has only M/2-1 entries
	{
		Try to redistribute, borrowing from sibling(adjacent node with same parent as L)
		If redistribute fails, merge L and sibling.
	}
}
If merge occurred, must delete entry(pointing to L or sibling) from parent of L.
```
##### B+Tree design choices

- Merge Threshold
- Non-unique Indexes
- Variable Length Keys
- Prefix Compression

##### B+Tree: Merge threshold
Some DBMSs don't always merge nodes when it is half full.

Delaying a merge operation may reduce the amount of reorganization.

##### B+Tree: Non-unique Indexes

- Approach #1 Duplicate Keys: Use the same leaf node layout but store duplicate keys multiple times
- Approach #2 Value Lists: Store each key only once and maintain a linked list of unique values.

##### B+Tree: Variable Length Keys

- Approach #1 Pointers: store the keys as pointers to the tuple's attribute
- Approach #2 Variable length nodes: the size of each  node in the B+Tree can vary; requires careful memory management
- Approach #3 Key Map: embed an array of pointers that map to the key+value list within the node.

##### B+Tree: Prefix compression

Since the keys in the inner nodes are only used to direct traffic, we store a minimum prefix that is needed to correctly route probes into the index other than the entire key.

##### B+Tree: Bulk Insert
The fastest/best way to build a B+Tree is to first sort the keys and then build the index from the bottom up.

### Observation
The easiest way to implement a **dynamic** order-preserving index is to use a sorted linked list

All operations have to linear search. Average Cost: O(N)

### Skip Lists
A collection of lists at different levels

- Lowest level is a sorted, singly linked list of all keys
- 2nd level links every other key
- 3rd level links every fourth key
- In general, a level has half the keys of one below it.

To insert a new key, flip a coin to decide how many levels to add the new key into. Provides approximate <font style="color:red;">O(log n)</font> search times.

#### Advantages:

- Uses less memory than a typical B+Tree id you don't include reverse pointers
- Insertions and deletions do not require rebalancing

#### Disadvantages:

- Not disk/cache friendly because they do not optimize locality references
- Invoking random number generator multiple times per insert is slow
- Reverse search is non-trivial

### Radix Tree
Uses digital representation of keys to examine prefixes one-by-one instead of comparing entire key

- The height of the tree depend on the length of keys
- Does not require rebalancing
- The path to a leaf node represents the key of the leaf
- Keys are stored implicitly and can be reconstructed from paths

#### Radix Tree: Binary comparable keys
not all attribute types can be decomposed into binary comparable digits for a radix tree

- **Unsigned integers**: Byte order must be flipped for little endian machines
- **Signed Integers**: Flip two's-complement so that negative numbers are smaller than positive
- **Floats**: Classify into group(neg vs. pos, normalized vs. denormalized), then store as unsigned interger
- **Compound**: Transform each attribute seperately.