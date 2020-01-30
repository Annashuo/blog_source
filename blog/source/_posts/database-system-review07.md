---
title: Database System Review 07
date: 2017-11-06 20:33:52
tags: database
categories: STUDY
---

### Query Optimization

- Heuristics / Rules
	- Rewrite the query to remove stupid / inefficient things
	- Does not require a cost model
- Cose-based search
	- Use a cost model to evaluate multiple equivalent plans and pick the one with the lowest cost.

#### Relational algebra equivalences (query rewriting)

- Selections:
	- Perform filter as early as possible
	- Break a complex predicate and push down
- Projections:
	- Perform them early to create smaller tuples and reduce intermediate results
	- Project out all attributes except the ones requested or required

#### Cost Estimation

- $N_{R}$:  # tuples
- $V(A,R)$: # of distinct values of attribute A
- Selection cardinality $SC(A,R)$: average number of records with a value for an attribute A given $N_{R}/V(A,R)$
- selectivity(<font style="color:red">sel</font>) of a predicate P is the fraction of tuples that qualify

##### Complex Predicates

- Equality Predicate: $A=constant \rightarrow sel(A=constant) = SC(P)\ /\ V(A,R)$
- Range: $sel(A>=a) = (A_{max}-a)/(A_{max}-A_{min})$
- Negation Query: $sel(not\ P) = 1\ -\ sel(P)$
- Conjunction: $sel(P1\ \wedge \ p2) = sel(P1)\ \cdot \ sel(P2)$
- Disjunction: $sel(P1\ \vee \ p2) = sel(P1)+sel(P2)-sel(P1\ \wedge \ p2)$

##### Result size estimation form joins

$R_{cols}\cap S_{cols}={A}$
$estSize \approx N_{R} \cdot N_{S}\ /\ max({V(A,S), V(A,R)})$

...to be continue...
Query Optimization is really hard...