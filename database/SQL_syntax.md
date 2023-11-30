order
```SQL
SELECT ... 
FROM ... 
WHERE ... 
GROUP BY ... 
HAVING ... 
ORDER BY ... 
LIMIT ... ;
```

**arithmetic operators**
`+`, `-`, `*`, `/` (fractional division),  `DIV` (integer division), `%` or `MOD`

**comparison operators**
`=`, `<>` or `!=`, `>`, `>=`, `<`, `<=`

# data types

**integer types**

|type|bytes|min value (sig)|max value (sig)|max value (unsig)|
|-|-|-|-|-|
|TINYINT|1|-2^7|2^7-1|2^8-1|
|SMALLINT|2|-2^15|2^15-1|2^16-1|
|MEDIUMINT|3|-2^23|2^23-1|2^24-1|
|INT|4|-2^31|2^31-1|2^32-1|
|BIGINT|8|-2^63|2^63-1|2^64-1|
`SIGNED` is the default value

`BIT(size)`
- size can range from 1 to 64
- default value of size is 1
- literals: `b'1001'` or `B'1001'` or `0b1001`
- normally print in hexadecimal, use `BIT(field)` to print actual bits
`BOOL/BOOLEAN`
- literals: `true`, `false`
- print `1` or `0`
- you must use the `IS` operator when comparing

**fixed point types**
store exact numeric data values

`DECIMAL(P,D)` means that the column can store up to P digits with D decimals
`P` is the precision that represents the number of significant digits ( between 1 and 65 )
`D` is the scale that represents the number of digits after the decimal point ( 0-30 )
`DEC`, `FIXED`, or `NUMERIC` are synonyms for `DECIMAL`

```SQL
amount DECIMAL(6,2); -- from 9999.99 to -9999.99
```

**floating point types**
represent approximate data types

|Type|Storage|Precision|Range|
|-|-|-|-|
|FLOAT|4 bytes|23 significant bits / ~7 decimal digits|10^+/-38|
|DOUBLE|8 bytes|53 significant bits / ~16 decimal digits|10^+/-308|
`REAL` is a synonym for `FLOAT`
`DOUBLE PRECISION` is a synonym for `DOUBLE`
both can be `SIGNED` or `UNSIGNED`

rounding
`DECIMAL()` return just the fractional part
`ROUND()` return the nearest even number
`CEIL()` or `CEILING()` round up a number
`FLOOR()` round down a number

**strings**

|string|bin|string maximum length|bin diff|
|-|-|-|-|
|`CHAR(size)`<br>`CHARACTER SET ascii`|`BINARY(size)`|0-255 chars|bytes|
|`VARCHAR(size)`|`VARBINARY(size)`|size < 2^16-1 chars|bytes|
|`TINYTEXT`|`TINYBLOB`|255 chars|bytes|
|`TEXT(size)`|`BLOB(size)`|size < 2^16-1 bytes|bytes of data|
|`MEDIUMTEXT`|`MEDIUMBLOB`|2^24-1 chars|bytes of data|
|`LONGTEXT`|`LONGBLOB`|2^32-1 chars|bytes of data|

`CHAR` and `BINARY` have fixed length while the others occupy only the effectively used disk space
the default size of `TEXT` is 65535
`TEXT` and `BLOB` cannot have a default value or be part of a key

**ENUM and SET** columns provide an efficient way to define columns that can contain only a given set of values.

An ENUM value must be one of those listed in the column definition, or the internal numeric equivalent thereof.
```SQL
CREATE TABLE t_e (
  e ENUM('a', 'b', 'c')
);
INSERT INTO t_e (e) VALUES ('a');
INSERT INTO t_e (e) VALUES (3); -- start at 1 -> means 'c'
```
A SET value must be the empty string or a value consisting only of the values listed in the column definition separated by commas.
```SQL
CREATE TABLE t_s (
s SET('a', 'b', 'c')
);
-- same as enum
INSERT INTO t_s (s) VALUES ('b');
-- plus
INSERT INTO t_s (s) VALUES ('a,c');
INSERT INTO t_s (s) VALUES ('');

INSERT INTO t_e (e) VALUES (3); -- means 'a,b'
-- transalate indexes into binary
-- 5 -> 101 -> a,c
-- 0 means the empty string
```

**time**

|type|format|range|to|
|-|-|-|-|
|DATE|YYYY-MM-DD|1000-01-01|9999-12-31|
|DATETIME|YYYY-MM-DD HH:MM:SS|1000-01-01 00:00:00|9999-12-31 23:59:59|
|TIMESTAMP||1970-01-01 00:00:01 UTC|2038-01-19 03:14:07 UTC|
|YEAR||1901|2155|
|TIME|HH:MM:SS|-838:59:59|838:59:59|

[functions](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html):
- `CURRENT_DATE()`
- `YEAR(date)`
- `MONTH(date)`
- `DAY(date)`

# data query language (DQL)
## select
```SQL
select distinct * from tbl;
-- eliminate duplicate rows from the result set
select if ( field >= 5 , "true", "false") as branch; -- alias

-- combine the results of two identically structured queries 
(select x, y from ...) union (select x, y from ...);
-- will strip out duplicates
-- if you needed so you could use keyword "UNION ALL"

(SELECT id FROM a) INTERSECT (SELECT id FROM b);
-- same as
SELECT a.id FROM a INNER JOIN b ON b.id = a.id

-- The CASE statement goes through conditions and returns a value when the first condition is met
SELECT field, 
CASE  
    WHEN qta > 30 THEN 'The quantity is greater than 30'  
    WHEN qta = 30 THEN 'The quantity is 30'  
    ELSE 'The quantity is under 30'  
END AS qta_text
FROM tbl;
```

## from

**sub-query**
```SQL
-- paese con maggior numero di abitanti
select name, population
from country
where population = (select max(population) from country);

-- numero stati appartenenti al continente con più stati
select max(states) as stati
from (
	select Continent, count(*) as states
	from country group by Continent
) as tabella;
```
nelle sub-queries è obbligatorio usare gli alias perché bisogna distinguere le tabelle della sub-query con quelle della main-query

A **common table expression** (CTE) is a named temporary result set that exists within the scope of a single statement and that can be referred to later within that statement, possibly multiple times.
```SQL
WITH HP AS (
	SELECT H.hospital_id, SUM(V.amount_paid) as amount_paid
	FROM Hospital H
	JOIN Visit V ON V.hospital_id = H.hospital_id
	GROUP BY H.hospital_id
),
second_cte AS (select 0)
-- The CTE cannot be followed by a semicolon like other SQL queries.
-- Instead it is followed by another query that uses it.
SELECT HP.hospital_id, HP.amount_paid
FROM HP
WHERE HP.amount_paid = (
	SELECT MAX(HP.amount_paid)
	FROM HP
);
```

To define a recursive CTE, the `RECURSIVE` keyword must be in its name
```SQL
WITH RECURSIVE fibonacci (n, fib_n, next_fib_n) AS (
	SELECT 1, 0, 1
	UNION ALL
	SELECT n + 1, next_fib_n, fib_n + next_fib_n
	FROM fibonacci
	WHERE n < 20
)
SELECT * FROM fibonacci;
```
## join
https://www.essentialsql.com/sql-joins/

TODO: https://www.guru99.com/joins-sql-left-right.html
- Inner Joins: Theta, Natural, EQUI
- Outer Join: Left, Right, Full 

Normalizing makes it really easy to update data but the price for this is that we have to piece the data back together to answer most of the questions we ask the database.
That is exactly why we need database joins.
A join condition is just matching one or more fields in one table to those in another.

(inner) join two tables together means looking for rows where the PK matches the correspond FK
se non si eliminano le copie il risultato avrà tante righe quante sono quelle della tabella contenente la FK

**SELF JOIN**
ha senso se una tabella si riferisce a se stessa

**CROSS JOIN**
Unlike other joins, a cross join uses no join conditions.
It produces a combination of all rows from the tables joined. 
In other words, it produces a "cartesian product" of the two tables.
```SQL
SELECT c.Color, s.Size
FROM Color c
CROSS JOIN Size s
```

![[cross_join.webp]]

TODO: [NON-EQUI JOIN](https://www.essentialsql.com/non-equi-join-sql-purpose/#)

**NATURAL JOIN**
match more than one field if possibile (all columns with the same name)
```SQL
SELECT *
FROM table_a
NATURAL JOIN table_b;
```

TODO: **THETA JOIN**

---
![](joins.png)
MySQL doesn't support the `FULL OUTER JOIN`

the `ON` keyword return a boolean, which represents whether the rows on either table are considered a match
LEFT JOIN: If the right-side table doesn’t produce any matches, then output the left-side data anyway. The output shows a NULL wherever the right side doesn’t match.
RIGHT JOIN: Same concept, but with the roles reversed. Therefore, the data on the right side is guaranteed to appear. For unmatched rows, NULL is printed in place of the left-side data.
INNER JOIN: Discard all the rows that don’t match. In other words, you won’t see NULLs as placeholders for the left or right table.

## where
```SQL
where field like "%str_";
-- % match any number of characters
-- _ exactly one
where field between x and y; -- >= 5 and <= y
-- not between means < x or > y
where field = "str"; -- and, or, () to write complex queries
-- `backticks` are used to use reserved words like variables
where field is (not) null; -- check nulls
```

The `EXISTS` operator is used to test for the existence of any record in a subquery, returns TRUE if the subquery returns one or more records
```SQL
SELECT * FROM table_name 
WHERE EXISTS (SELECT * FROM table_name WHERE condition);
```

The `ANY` and `ALL` operators allow you to perform a comparison between a single column value and a range of other values.

`ANY` means that the condition will be true (returns a boolean) if the operation is true for any of the values in the range

```SQL
SELECT field(s)
FROM tbl  
WHERE field comparison_operator ANY  
  (SELECT f2  FROM tbl2  WHERE cond2);
```

The `ALL` operator:
- returns TRUE (returns a boolean) if ALL of the subquery values meet the condition
- is used with `SELECT`, `WHERE` and `HAVING` statements

```SQL
SELECT ALL field(s)
FROM tbl
WHERE condition;
```

TODO: regex, full text search
## group by & having
using `group by ... having` to filter aggregate records is analogous to using `select .. where` to filter individual records
`having` understand aliases
filtrare non le singole tuple ma i gruppi

```SQL
SELECT GROUP_CONCAT(Score ORDER BY Score desc SEPARATOR ' ') AS Grades
FROM Grade
GROUP BY Name;
-- | Adam | C+ B A- A+ |

-- numero di lingue parlate in ogni stato in cui si parlano più di 9 lingue
SELECT country.Name, COUNT(*) AS numero_lingue 
FROM countrylanguage, country
WHERE countrylanguage.CountryCode = country.Code
GROUP BY country.Name
HAVING Numero_lingue > 9;
```

ha senso usare il nome dei campi insieme agli operatori di aggregazione solo se si usa il `GROUP BY` sul nome dei campi nella `target list`

```SQL
SELECT department, MIN(salary) AS "Lowest salary"
FROM employees
GROUP BY department;
```
Nella grouping list devono comparire tutti e soli gli attributi che sono nella target list (aggregazioni escluse)

TODO: HAVING EVERY/ANY

**aggregate functions**
`COUNT()` returns the number of rows
`SUM()` return the sum of the selected column
`AVG()` returns the average value of a column of numeric value
`MAX()/MIN()` returns the highest/lowest value of a certain column or expression

## order by & limit

NULLS precede non-NULLs
the default is `ASC` ( lower to higher ) ( the opposite is `DESC`)
strings (`varchar`, etc) are ordered according the `COLLATION` of the declaration
`ENUMs` are ordered by the declaration order of its strings

```SQL
order by field asc limit 3;
-- without the order the rows will be unpredictable
limit x, y; -- start from x+1 record and select only y rows

-- multiple columns
-- the starting columns have the highest priority
ORDER BY article_rating DESC, article_time DESC;
```

# data control language (DCL)

```bash
# access like root to the localhost service
sudo mysql --user=root mysql
# access normally
mariadb -u root -p"root" -h localhost
```

```SQL
-- print users table
SELECT user,host,password from mysql.user;
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
-- grant all privileges
GRANT ALL PRIVILEGES ON db_name.* TO 'username'@'%' WITH GRANT OPTION;

SET PASSWORD FOR 'username'@'host' = PASSWORD('pass');
FLUSH PRIVILEGES;
DROP USER 'username'@'host';
```

import data from file
SQL: `SOURCE ./script.sql;`
bash: `mariadb -u username -p db_name < script.sql`

export entire database to file
bash: `mariadb-dump -u username -p db_name > filename.sql`

export single tables to file
bash: `mariadb-dump -u username -p db_name table1 table2 > file.sql`

# data definition language (DDL)

```SQL
SHOW DATABASES;
CREATE DATABASE db_name;
USE db_name;
SHOW TABLES;
USE table_name;
DESCRIBE table_name;
DROP TABLE table_name;
SET @var_name = 'str';
EXIT;
```

```SQL
create table Nation (
id int auto_increment not null,
name text not null,
constraint Nation_pk primary key (id)
);
CREATE TABLE Person (
id INT AUTO_INCREMENT,
name TEXT NULL,
gender ENUM ('male', 'female') NOT NULL DEFAULT 'male',
nation_id INT NOT NULL,
UNIQUE (name),
PRIMARY KEY (id),
FOREIGN KEY (nation_id) REFERENCES Nation (id) ON DELETE CASCADE
);
-- naming a constraint is optional

ALTER TABLE Person DROP name;
ALTER TABLE Person ADD name TEXT NULL;
ALTER TABLE Person MODIFY name TEXT NOT NULL;

```

**referential constraint**
cascade
	(on delete) will automatically delete the matching rows in the child table propagate the deletion performed to its related tables
	(on update) when an update occurs in the parent table it must automatically update the matching rows in the child table with the new value
set null
	the foreign key column from the child table will be set to `NULL` when the parent record gets updated or deleted
	(make sure that you have declared the `*_id` column in the table as `NULL`able)
restrict or "no action" (is the default type)
	it rejects the delete or update operation for the parent table if the table has associated children

**view**
```SQL
CREATE TABLE t (id INT AUTO_INCREMENT, qty INT, price INT);
INSERT INTO t VALUES(3, 50);
CREATE VIEW v AS SELECT qty, price, qty*price AS value FROM t;
SELECT * FROM v;
DROP VIEW v;
```
diff with temporary tables

# data manipulation language (DML)

```SQL
-- Description Model Language
INSERT INTO Nation (name) VALUES ('france'), ('spain');
INSERT INTO Person (name, gender, nation_id) 
VALUES ( 'michele', 'male', 1 );

-- modify the existing records in a table
UPDATE Nation SET name='italy' WHERE id=1

-- remove
DELETE FROM Nation WHERE id=2;
```

# transaction control language (TCL)

TCL commands are used to manage transactions within a database. They are used to control the changes made to a database by committing or rolling back transactions. Some common TCL commands include:

```sql
COMMIT, ROLLBACK, SAVEPOINT
```
