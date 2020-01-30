---
title: Database System Review 02
date: 2017-09-14 18:00:22
tags: database
categories: STUDY
---
This contains functional dependencies and normal form.

<!--more-->

### Functional Dependencies (FD)
#### Definition

A Functional Dependency (FD) is a form of a constraint, part of a relation's schema to define a valid instance.

$$X \rightarrow Y$$
The value of X functionally defines the value of Y. For example, if two tuples$(t_{1},t_{2})$ agree on the X attribute, then they must agree on the Y attribute too.

$$(X \rightarrow Y) \, + \, (X \rightarrow Z) \, \Rightarrow \, (X \rightarrow YZ)$$
$$(XY \rightarrow Z) \, \not\Rightarrow \, (X \rightarrow Z) \, + \, (Y \rightarrow Z)$$

#### Definning FDs in SQL
eg.

$$FD_{1}: sid \rightarrow name$$
$$FD_{2}: sid \rightarrow address$$

```
CREATE ASSERTION student-name-addr 
  CHECK (NOT EXISTS
    (SELECT * FROM students AS s1, 
                   students AS s2
      WHERE s1.sid = s2.sid
        AND((s1.name <> s2.name
        	OR (s1.address <> s2.address)));
```
#### Armstrong's Axioms

- Reflexivity: $X \supseteq Y \Rightarrow X \rightarrow Y$
- Augmentation: $X \rightarrow Y \Rightarrow XZ \rightarrow YZ$
- Transitivity: $(X \rightarrow Y) \wedge (Y \rightarrow Z) \Rightarrow (X \rightarrow Z)$
- Union: $(X \rightarrow Y) \wedge (X \rightarrow Z) \Rightarrow (X \rightarrow YZ)$
- Decomposition: $(X \rightarrow YZ) \Rightarrow (X \rightarrow Y) \wedge (X \rightarrow Z)  $
- Pseudo-transitivity: $(X \rightarrow Y) \wedge (YW \rightarrow Z) \Rightarrow (XW \rightarrow YZ)$

#### Closure (F+)
Given a set F of FDs, closure F+ is the set of all implied FDs.

To check $X \rightarrow A$:

1. compute X+
2. check if $A \in X+$

#### Canonical Cover ($F_{c}$)
Given a set F of FDs, canonical cover $F_{c}$ is the minimal set of all FDs.

$F_{c}$ properties:

1. The right-hand side(RHS) of every FD is a single attribute
2. $F_{c}$ is identical to the closure F+
3. $F_{c}$ is minimal(we cannot eliminate any attribute from the LHS or RHS of a FD)
4. $F_{c}$ LHS usually unique

### Relational Model Keys

- Super key: any set of attributes in a relation that functionally determines all attributes in the relation (随意组合attribute，只要组合出来的结果可以决定其他所有attribute，这个组合就是super key)
- Candidate key: any super key such that the removal of any attribute leaves a set that does not functionally determine all attributes (super key里面元素最少的一个是candidate key)
- primary key = candidate key

### Decomposition Goals

- Lossless Joins (**mandatory**): 要保证lossless joins，找出最有决定性的LHS的attribute，以它作为跨越多个表的attribute进行拆分
- Dependency Preservation: 最好同一个FD里的attribute在拆分后的同一个表内
- Redundancy Avoidance

#### Lossless Joins
For the decomposition $R={R_{1}\cup R_{n}}$, check whether $(R_{1} \cap R_{2})\rightarrow R_{1}$ **or** $(R_{1} \cap R_{2})\rightarrow R_{2}$

#### Dependency Preservation

To test whether the decomposition $R={R_{1},..R_{n}}$ preserves the FD set F:

1. Compute F+
2. Divide F+ into sets of only covered by ${R_{1},..,R_{n}}$
3. Compute G as union of those sets
4. Compute G+
5. If F+=G+, then ${R_{1},..,R_{n}}$ is dependency preserving

#### Redundancy Avoidance
For an $X \rightarrow Y$ covered by$R_{n}$, X should be a super key of $R_{n}$

### Normal Forms
A Normal Form is a characterization of a decomposition in terms of the properties that satisfies when putting the relations back together.

#### First Normal Form(1NF)

1. All value must be atomic
2. No repeating groups

#### Second Normal Form(2NF)

1. 1NF
2. Non-key attributes fully depend on the candidate key

Decomposition后，如果一个表里除了一个FD的LHS和RHS以外还有其他的attribute，就提出LHS和RHS成立一个新的表

#### Boyce-codd Normal Form(BCNF)

- +no lossless joins
- +no redundancies
- -dependency perserving

**for every FD in a relation R, LHS must be super key**

A relation R with FD set F is in BCNF if for every $X \rightarrow Y$ in F+:

- $X \rightarrow Y$ is trivial (that is, $Y \subseteq X$), or
- X is a super key

##### BCNF decomposition algorithem(Given a relation R and a FD set F):

- Compute F+
- $Result \leftarrow {R}$
- While $R_{i} \in Result$ not in BCNF, do:
	- Choose$(X \rightarrow Y)\in F+$ such that $(X \rightarrow Y)$ is covered by $R_{i}$ and $X \not\rightarrow R_{i}$ 
	- Decompose $R_{i}$ on $(X \rightarrow Y)$ into $R_{i,1} \leftarrow X \cup Y$ and $R_{i,2} \leftarrow R_{i}-Y$

##### Problem with BCNF

After decomposed R into BCNF relations ${R_{1},...,R_{n}}$ with their own ${FD_{1},....FD_{n}}$, we can reconstruct R from ${R_{1},...,R_{n}}$ but cannot reconstruct FD from ${FD_{1},....FD_{n}}$.

#### Third Normal Form(3NF)

- +no lossless joins
- no redundancies
- dependency perserving

A relation R with FD set F is in 3NF if for every $X \rightarrow Y$ in F+:

- $X \rightarrow Y$ is trivial, or
- X is a super key, or
- Y is part of a candidate key

##### 3NF decomposition algorithem(Given a relation R and a FD set F):

- Compute $F_{c}$
- $Result \leftarrow \phi$
- For $(X \rightarrow Y)\in F_{c}$, add a relation $R_{i}(X,Y)$ to Result
- If result is not lossless, add a relation with an appropriate key

##### 3NF and BCNF

- both lossless
- BCNF lose dependency preservation
- 3NF lose redanduncy avoidance
