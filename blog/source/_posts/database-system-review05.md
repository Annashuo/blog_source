---
title: Database System Review 05
date: 2017-10-10 10:33:25
tags: database
categories: STUDY
---
Query processing includes processing models, access models and expression evaluation

<!--more-->
### Query plan
The operators are arranged in a tree. Data flows from the leaves toward the root.

### Processing Methods
A DBMS's processing model defines how the system executes a query plan.

Three approaches:

- Iterator Model
- Materialization Model
- Vectorized / Batch Model

#### Iterator Model(Volcano Model) -- Top-down plan processing
Each query plan operator implements a next function.

- On each invocation, the operator returns either a singke tuple or a null marker if there are no more tuples
- The operator implements a loop that calls next on its children to retrieve their tuples and then process them.

This allows for tuple pipelining. Some operators will block until children emit all of their tuples.

#### Materialization Model -- Bottom-up plan processing
Each operator processes its input all at once and tehn emits its output all at once.

- The operator "materializes" it output as a single result.
- The DBMS can push down hints into avoid scanning too many tuples.

Better for PLTP workloads because queries typically only access a small number of tuples at a time.

- Lower execution / coordination overhead.
- More difficult to parallelize.

No good for OLAP queries with large intermediate result.

#### Vectorization Model
Like Interator Model, each operator implements a next function

Each operator emits a <u>batch</u> of tuples instead of a single tuple.

- The operator's internal loop processes multiole tuples at a time.
- The size of batch can vary based on hardware or query properties.

Ideal for OLAP queries

- Greatly reduces the number of invocations per operator
- Allows dor operators to use vectorized(SIMD) instructions to process batches of tuples.

Processing Model | Iterator | Materialization | Vectorized
:--:|:--:|:--:|:--|
Direction | Top-Down | Bottom-Top | Top-Down
Emits | Single Tuple | Entire Tuple Set | Tuple Batch
Target | General Purpose | OLTP | OLAP

### Acess Method
A access method is a way that the DBMS can access the data stored in a table.(Not defined in relational algebra)

Three basic approaches:

- Sequential Scan
- Index Scan
- Multi-Index / "Bitmap" Scan

#### Sequential Scan
For each page in the table:

- Retrive it from the buffer pool.
- Iterate over each tuple and check whether to include it

The DBMS maintain an internal **cursor** that tracks the last page / slot it examined.

Sequential Scan Optimizations:

- Prefetching
- Paralleization
- Zone Maps
- Buffer POll Bypass
- Heap Clustering

##### Zone Maps
Pre-computed aggregates(like min, max, avg, sum, count) for the attribute values in a page.

DBMS can check the zone map first to decide whether it wants to access the page.

##### Buffer Pool Bypass(Light Scan)
The sequential scan operator will not store fetched pages in the buffer pool to avoid overhead.

- Memory is local to running query.
- Works well if operator need to read a large sequence of pages that are contiguous on disk.

##### Heap Clustering
Tuple are sorted in the heap's pages using the order specified by a clustering index.

If the query accesses tuples using the clustering index's attributes, then the DBMS can jump directly to the pages that it needs.

#### Index Scan
The DBMS picks an index to find the tuples that the query needs.

Which index to use depends on:

- What attributes the index contains
- What attributes the query references
- The attributes's value domains
- Predicate composition
- Whether the index has unique or non-uninque keys

#### Multi-index Scan(Bitmap Scan in PostgreSQL)
If there are multiple indexs that the DBMS can use for a query:

- Compute sets of record ids using each matching index
- Combine these sets bases on the query's predicates(union vs. intersect)
- Retrieve the record and apply any remaining terms

##### Index Scan Page Sorting
Retrieve tuples in the order that appear in an unclustered index is inefficient.

The DBMS can first figure out all the tuples that it needs and then sort them based on their page id.

### Expression Evaluation
The DBMS represents a WHERE clause as an expression tree.

The nodes in the tree represent different expression types:

- Comparisons(=,<,>,!=)
- Conjunction(AND), Disjunction(OR)
- Arithmetic Operators(+,-,*,/,%)
- Constant Values
- Tuple Attribute References

Expression Tree is slow because the DBMS traverses the tree and for each node that it visits it has to figure out what the operator needs to do.

Better approach is to just evaluate the expression directlt(JIT compilation)