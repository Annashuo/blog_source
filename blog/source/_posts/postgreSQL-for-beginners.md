---
title: PostgreSQL_for_beginners
date: 2017-08-25 13:57:15
tags: postgreSQL
categories: STUDY
---
PostgreSQL for Beginners
<!--more-->

#### Server Service
You can install multiple servers in a physical server using different ports and also have different locations to store data. 
#### Database
A database is container of all the objects such as tables, functions, sequences and trigger.

- Table
- Schema: 

	- A logical container of the tables and other database objects.

	- It is possible to assign permission to schemas, so you can restrict who can access the objects.

	- You can have multiple schemas in a database.

- Tablespace: 

	- Storage location where data is stored.

	- By default, PostgreSQL provides two tablespaces: *pg_default* and *pg_global*

		*pg_default* is used for storing user data.

		*pg_global* is used for storing system data.

- Views

	- Virtual tables that are used to basically simplify complex queries.
 
	- Used to apply security for a set of records. You can use view to hide information or protect certain parts of a table from others to see, so you can create a view from a table.

- Functions

	- Block of reuseable SQL Code.

	- Returns acalar value of a list of records.
	- Return composite objects.

- Cast 

	- Convert one data type into another data type. 
	- It is used with functions to perform conversion.
	- You can create custom casts to overide the default.

- Operator

	- A symbolic function
	- You can define your own custom operators

- Sequences

	- Used to manage auto-increment columns

- Extension

	- Used to wrap other objects such as casts, indexes, functions and others into a single unit

#### PostreSQL Fundamentals

##### DATA Types

- Boolean: True, False, NULL
- Character: Char(n), Varchar(n), text
- Number: small integer(smallint), integer(int), serial(autoincrementing integer), float(n), real or float8, numeric or numeric(p,s)

![](https://github.com/Annashuo/hello-world/blob/master/numerial_data_type.png?raw=true)
	
- Temporal: 

	- **date** store date values only
	- **time** store time of the day values
	- **timestamp** store data and time
	- **interval** store periods of time
	- **timestamptz** store both timestamp and time zone data
	
- Array
- Special types

	- **box**: a rectangular box
	- **line**: a set of points
	- **point**: a geometric pair of numbers
	- **lseg**: a line segment
	- **polygon**: a closed geometric
	- **inet**: an IP4 address
	- **macaddr**: a MAC address

##### UNIQUE constraints
Ensures values in a column or group of columns are unique in a table.

```
	CREATE TABLE person(
		id serial PRIMARY KEY,
		first_name VARCHAR(50),
		last_name VARCHAR(50),
		email VARCHAR(50) UNIQUE
	);
```

##### WHERE
Filtering rows returned from SELECT query
```
	SELECT column1, column2
	FROM table_name
	WHERE conditions;
```
##### SELECT 
Query data from all columns

```
	SELECT *
	FROM table_name;
	
	SELECT column1, column2
	FROM table_name;
```

##### DISTINCT
Removing duplicate records

```
	SELECT DISTINCT column1, column2
	FROM table_name
	ORDER BY
	column1 ASC,
	column2 DESC;
```

##### ORDER BY
Sort data returned by SELECT statement, default is ASC.

```
	SELECT DISTINCT column1, column2
	FROM table_name
	ORDER BY
	column1 ASC,
	column2 DESC;
```

##### GROUP BY
Used to divide rows return ed from a SELECT statement into groups.

You can apply aggregate function e.g SUM and COUNT

```
	SELECT column1, aggregate_function(column2)
	FROM table1
	GROUP BY column1;
```

##### AGGREGATE FUNCTION
Used to perform calculations on data

They return a single value calculated from values in a column

FUNCTION | DESCRIPTION 
:----:|:----:
AVG() | Return average value
COUNT() | Return number of rows
MAX() | Return the largest value
MIN() | Return smallest value
SUM() | Return the sum
FIRST() and LAST() | Return first and last value

##### HAVING 
Used to in conjunction with GROUP BY clause

Used filter group rows that do not satisfy a specified condition.

```
	SELECT column1, aggregate_function(column2)
	FROM table_name
	GROUP BY column1
	HAVING condition;
```

#### TRUNCATE and DROP
TRUNCATE removes all data from a table

DROP delete the table from the database

```
	TRUNCATE TABLE table_name;
	DROP TABLE table_name;
```

#### Postgres CRUD Operations
##### Create database and table

```
	CREATE TABLE table_name(
		column1_name datatype column_constraint,
		column2_name datatype column_constraint,
	);
```

##### Read table

```
	SELECT columns
	FROM table_name;
```
##### Subquery 
Allows you to construct complex queries.

Query nested inside another query such as SELECT.

To construct a subquery the second query is placed in brackets.

The WHERE clause is used as expression in the subquery.

```
	SELECT "column_name1"
	FROM "table_name1"
	WHERE "column_name2"[Comparison Operator]
	(SELECT "column_name3"
	FROM "table_name2"
	WHERE "condition");
```
##### UPDATE

```
	UPDATE table_name
	SET column1=value1,
	    column2=value2
	WHERE condition;
```

##### DELETE

```
	DELECT FROM table_name
	WHERE condition;
```

##### INSERT

```
	INSERT INTO table_name(column1, column2)
	VALUES(value1, value2);
```

#### OPERATOR
##### Comparison

![](https://github.com/Annashuo/hello-world/blob/master/postgres_operator.png?raw=true)

##### BETWEEN OR NOT BETWEEN

Values can be numbers, text or dates

Included in the WHERE clause

```
	SELECT column_names
	FROM table_name
	WHERE column_name (NOT) BETWEEN value1 AND value2;
```

##### NOT

```
	SELECT coloumn_names
	FROM table_name
	WHERE NOT condition 
```

##### LIKE

Match patten in a column.

% wildcard allows you to match string of any length characters including zero.

_ wildcard allows you to match a single character

```
	SELECT column1, column2
	FROM table_name
	WHERE column LIKE patten;
```

##### OR & AND

AND operator and OP operator can be used in SELECT, INSERT, UPDATE, DELECT.

Use parenthesis () when combining conditions.

```
	SELECT column
	FROM table_name
	WHERE condition AND/OR condition
```

##### LIMIT

Gets a subset of row returned by a query

```
	SELECT * 
	FROM table_name
	LIMIT n;
```

##### IN

In a **list**

```
	SELECT column_name
	FROM table_name
	WHERE column_name IN (value1, value2)
```

##### UNION & UNION ALL

Used to combine result sets of multiple queries into a single result.

Without **ALL**, removes all duplicate rows. With **ALL**, does not remove duplicate rows.

Both queries must return same number of columns.

The corresponding columns in the queries must have compatible data type.

```
	SELECT column1, column2
	FROM table1
	UNION
	SELECT column1, column2
	FROM table2;
```

##### INTERSECT

Used to combine result set of two or more SELECT statements into a single result.

The INTERSECT operator returns all rows in both result sets.

The number of columns that appear in the SELECT statement must be the same.

The data types of the columns must be compatible.

```
	SELECT column1, column2
	FROM table1
	INTERSECT
	SELECT column1, column2
	FROM table2;
```

##### EXCEPT

Retuen rows by comparing the result sets of two or more queries.

Return rows in the firdt query not present in output of the second query.

Returns distinct rows from the first(left) query not in out of the second(right) query.

The number of colums and their orders must be the same in both queries.

The data types of the respective columns must be compatible.

```
	SELECT column1, column2
	FROM table1
	WHERE conditionA
	EXCEPT
	SELECT column1, column2
	FROM table2
	WHERE conditionB;
```

#### JOINS

Used to retrieve data from multiple tables.

Joins is performed when two or more tables are joined in a SQL statement.

Tables participating in the join should have related columns between them.

Types of Joins

- INNER JOIN
- LEFT JOIN
- OUTER JOIN
- CROSS JOIN: produce cartesian product of rows from combind joined table. Tables can have no matching columns
- NATURAL JOIN: create an implicit join based on matching column names in the tables. It can be INNER JOIN, LEFT JOIN, RIGHT JOIN. Default is INNER JOIN.

```
	SELECT column_name(s)
	FROM tableA
	(INNER/LEFT/OURTER) JOIN tableB ON tableA.column = tableB.column;
	
	SELECT *
	FROM table1 
	(CROSS/NATURAL) JOIN table2;
```
INNER JOIN:
![](https://github.com/Annashuo/hello-world/blob/master/INNER_JOIN.png?raw=true)

LEFT JOIN:
![](https://github.com/Annashuo/hello-world/blob/master/LEFT_JOIN.png?raw=true)

OURTER JOIN:
![](https://github.com/Annashuo/hello-world/blob/master/OUTER_JOIN.png?raw=true)




