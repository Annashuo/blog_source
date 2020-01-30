---
title: Database System Review 09
date: 2017-11-07 15:39:00
tags: database
categories: STUDY
---
##<center> Embedded Database Logic</center>

### User-defined Functions(UDF)

- Return Types:
	- Scalar Functions: return a single data value.
	- Table Functions: return a single result table.
- Computation Definition:
	- SQL functions
	
	eg.
	
	```
	CREATE FUNCTION get_foo(int)
						RETURNS foo AS $$
		SELECT * FROM foo WHERE foo.id = $1;
	$$ LANGUAGE SQL;
	```
	- External programming language


### Storage Procesures
A <u>storage procedure</u> is a self-contained function that performs more complex logic inside of the DBMS.

- Can have many input/output parameters
- Can modify the database table/structures
- Not normally used with a SQL query

#### Storage proceduce vs. UDF

- A UDF is meant to perform a subset ofread-only computation with a query.
- A storage proceduce is meant to perform a complete computation that is independent of a query. 

### Triggers
A <u>trigger</u> instruct the DBMS to invoke a UDF when some event occurs in the database.

It contains:

- What type of event will cause it to fire
	
	INSERT, UPDATE, DELETE, TRUNCATE, CREATE, ALTER, DROP
	
- The scope of the event

	TABLE, DATABASE, VIEW, SYSTEM
	
- When it fires relative to that event
	- Before the statement executes
	- After the statement executes
	- Before each row that the statement affects
	- After each row that the statement affects
	- Instead of the statement

### Change Notifications
A <u>change notification</u> is like a trigger except that the DBMS send a message to an external entity that something notable has happened in the database.


### User-defined Types
A <u>user-defined type</u> is a special data type that ts defined by the application developer that the DBMS can stored natively.

### Views
Creates a "virtual" table containing the output from a SELECT query.

Mechanism for hiding data from view of certain users.

Can be used to simplify a complex query that is executed often but won't make it faster though.

#### VIEW vs. SELECT INTO

- VIEW: dynamic results are only materialized when needed.
- SELECT...INTO: creates static table that does not get updated when original table gets updated.

#### Materialized View
Creates a view containing the output from a SELECT query that is automatically updated when the underlying tables change.