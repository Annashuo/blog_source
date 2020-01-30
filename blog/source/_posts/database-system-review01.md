---
title: Database System Review 01
date: 2017-09-14 11:20:49
tags: database
categories: STUDY
---
This is the introduction of database, relational database and SQL.

<!--more-->

### Instruction
#### Database
Organized collection of interelated data that models some aspect of the real-world.

- data integrity
- implementation
- durability

#### Database Management System
A DBMS is software that allows applications to store and analyse information in a database

##### DBMS types by Target Workloads:

- On-line Transaction Processing(OLTP): Fast operations that only read/update a small amount of data each time
- On-line Analytical Processing(OLAP): Complex queries that read a lot of data to compute aggregates
- Hybrid Transaction + Analytical Processing: OLTP + OLAP

##### DBMS types by Data Model

**Data model**: collection of concepts form describing the data in a database.

**Schema**: a description of a particular collection of data, using a given data model.


- Most DBMSs data model
	- Relational  
- NoSQL data model
	- Key/Value
	- Graph
	- Document
	- Column-family
- Machine Learning data model
	- Array/Matrix
- Obsolete / Rare
	- Hierarchical
	- Network

### Relational Model

1. Structure

	**Relation**: unordered set that contain the relationship of attributes that represent entities.

	**Tuple**: a set of attribute values(domain) in the relation

	- values are (normally) atomic/scalar
	- The special value <font color=red>NULL</font> is a member of every domain

2. Integrity Constraints:

	- Primary Keys: Uniquely identifies a single tuple
	- Foreign Keys: Specifies that an attribute from one relation has to map to **a** tuple in another relation

3. Manipulation

	Data Manipulation Language(DML)

	- Procedural(**Relational Algebra**): The query specifies the strategy the DBMS should use to find the desired result
	- Non-Procedural(**Relational Calulus**): The query specifies only what data is wanted and not how to find it.

#### Relational Algebra

1. $\sigma$

	$\sigma _{predicate}(R)$
	
	```
	SELECT * 
	FROM R
	WHERE predicate;
	```

2. $\pi$

	$ \pi _{A_{1},A_{2},...,A_{n}} (R) $
	
	```
	SELECT A1, A2, ... An
	FROM R;
	```
	
3. $U$

	$(R\, U \, S)$
	
	```
	SELECT * FROM R
	UNION
	SELECT * FROM S;
	```
	
4. $\cap$

	$(R\, \cap \, S)$
	
	```
	SELECT * FROM R
	INTERSECT
	SELECT * FROM S;
	```
	
5. $-$

	$(R\, - \, S)$
	
	```
	SELECT * FROM R
	EXCEPT
	SELECT * FROM S;
	```
	
6. $\times$

	$(R\, \times \, S)$
	
	```
	SELECT * FROM R
	CROSS JOIN
	SELECT * FROM S;
	
	SELECT * FROM R, S;
	```

7. $\bowtie$

	$(R\, \bowtie \, S)$
	
	```
	SELECT * FROM R
	NATURAL JOIN
	SELECT * FROM S;
	```
	*Join type*:
	
	- Cross join
	- Inner join (<font color=red>!! NATURAL JOIN AND INNER JOIN have different result</font>)
		- Natural join$(R\, \bowtie \, S)$

			```
			SELECT * FROM R NATURAL JOIN S;
			```
		- Theta join$(R\, \bowtie_{\theta} \, S)$
		
			```
			SELECT * FROM R INNER JOIN S ON R.C1 = S.C1;
			```
		- Semi join$(R\, \ltimes \, S)$
		
			```
			SELECT * FROM R WHERE EXISTS (SELECT * FROM S WHERE R.C1 = S.C1);
			```
		- Anti join$(R\, \triangleright \, S)$

			```
			SELECT * FROM R WHERE NOT EXISTS (SELECT * FROM S WHERE R.C1 = S.C1);
			```
	- Outer join
		- Left outer join $(R\, ⟕ \, S)$

			```
			SELECT * FROM R LEFT OUTER JOIN S ON R.C1 = S.C1;
			```
		- Right outer join $(R\, ⟖ \, S)$

			```
			SELECT * FROM R RIGHT OUTER JOIN S ON R.C1 = S.C1;
			```
		- Full outer join (Union)

8. extra operators
	- Rename $\rho$
	- Assignment $R\leftarrow S$
	- Duplicate Elimination $\delta$
	- Agregation $\gamma$
	- Sorting $\tau$
	- Division $R\div S$ 

#### Relational Language

- Data Manipulation Language(DML)
- Data Definition Language(DDL) 

#### Aggregation

- AVG
- MIN
- MAX
- SUM
- COUNT

COUNT, SUM, AVG support DISTINCT.

Output of other columns outside pf an aggregate is undefined.

#### Group by
Non-aggreagated values in SELECT output clause must appear in GROUP BY clause.

#### HAVING
Filters output results for GROUP BY.

#### String operations

- LIKE: string matching
	- "%": matches any substring(including empty)
	- "_": match any one character
- SUBSTRING(STRING, BEGIN, END)
- UPPER
- LOWER
- ||

#### Output control

- Order by
- LIMIT OFFSET

#### Nested query

- ALL
- ANY
- IN
- EXISTS

#### Window function

```
SELECT ... FUNC-NAME(...) OVER (...)
FROM table
```
FUNC-NAME:

- ROW_NUMBER(): number of the current row
- RANK(): order position of the current row

OVER: 

- PARTITION BY: to specify group
- ORDER BY

#### Common Table Expressions
```
eg. print the sequence of numbers from 1 to 10

with recursive cteSource(counter) as(
	(select 1)
	union all
	(select counter+1 from cteSource
	where counter < 10)
)
select * from cteSource;
```
