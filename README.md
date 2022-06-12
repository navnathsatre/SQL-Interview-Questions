# SQL-Interview-Questions

## 1. Create database and table
```
CREATE DATABASE IF NOT EXISTS db1;
```
USE db1;
```
CREATE TABLE DEPARTMENT(ID INT NOT NULL,
                        NAME VARCHAR(250) NOT NULL
                        ,LOCATION VARCHAR(250) ); 
```
SHOW TABLES;
 
DESC DEPARTMENT;

## 2. Create Table with same structure of different table
```
CREATE TABLE department_copy 
AS (SELECT * FROM DEPARTMENT WHERE 1=2); -- same structure
```
 DESC DEPARTMENT;
 
 DESC department_copy;

## 3. Create table with same structure and same data of another table
```
CREATE TABLE Emp Select * from department;
```
## 4. Find 2nd/3rd/nth Highest salary
__—Subquery__
```
SELECT MAX(salary) 
FROM emp 
WHERE (salary<(SELECT MAX(salary) FROM emp)
WHERE salary<(SELECT MAX(salary)FROM emp)); — 3rd highest
```
__—limit__
```
SEELCT salary FROM emp ORDER BY salary DESC LIMIT 2,1   ;  -- 3rd highest
— LIMIT N-1, no. of records 
```
__—limit and offset__
```
SELECT salary FROM emp ORDER BY salary DESC LIMIT 1, OFFSET 2 ;  — 3rd highest
— LIMIT no. of records,  OFFSET N-1
```
__—distinct__
```
SELECT salary FROM emp e1
WHERE (2) = (SELECT COUNT(DISTINCT(salary) FROM emp e2 WHERE e2.salary > e1.salary);
```
## 5. Find all Employee who also hold Managerial position 
```
SELECT * FROM emp
WHERE emp_id IN (SELECT mgr_id FROM emp);
```
## 6. Find the name of employee that begins with ‘A’
```
SELECT name FROM emp 
WHERE name LIKE ’A%’;           — for any: %  for specific: _
```
## 7. Display Current Date
```
SELECT CURRENT_DATE;
SELECT CURRENT_DATE();
SELECT CURDATE();
SELECT DATE(NOW());
SELECT DATE(CURRENT_TIMESTAMP());
```
## 8. Fetch alternate records
```
SELECT * FROM emp WHERE id % 2 = 0  — even records
SELECT * FROM emp WHERE ID % 2 = 1. — odd records
```
```
SELECT * FROM (SELECT *, ROW_NUMBER() OVER (ORDER BY id) AS RowNo FROM emp e)
WHERE e.RowNo % 2 = 0;
SELECT * FROM (SELECT *, ROW_NUMBER() OVER (ORDER BY id) AS RowNo FROM emp e)
WHERE e.RowNo % 2 = 1;
```
## 9.  Fetch common records from two table
```
SELECT * FROM emp1
INNER JOIN emp2 
ON emp1.id = emp2.id ;
```
## 10. Find Duplicate rows in table
```
SELECT col, COUNT(*)
FROM emp 
GROUP BY col
HAVING COUNT(*)>1;
```
## 11. Remove Duplicate rows in table
```
DELETE e1 FROM emp e1
INNER JOIN emp e2
WHERE e1.id < e2.id AND e1.name = e2.name;
```
## 12.  Find nth record in table
```
SELECT * FROM emp LIMIT 9, 1.                 — 10th record
SELECT * FROM emp LIMIT 1 OFFSET 9    — 10th record
```
## 13. Find Last 5 records
```
(SELECT * FROM emp ORDER BY id DESC LIMIT 5) ORDER BY id ASC;
```
```
SELECT * FROM emp
WHERE id > (SELECT MAX(id) -5 FROM emp);
```
```
SELECT * FROM emp
WHERE id > (SELECT COUNT(*) FROM emp) -5;
```

## 14. Find First and Last record without using LIMIT
```
SELECT * FROM emp
WHERE id = (SELECT MIN(id) FROM emp);
```
```
SELECT * FROM emp
WHERE id = (SELECT MAX(id) FROM emp);
```

## 15 Find Distinct records without using DISTINCT keyword
```
SELECT dept_id FROM emp GROUP BY dept_id;       — using GROUP BY
```
```
SELECT salary FROM emp
UNION										  — using UNION
SELECT salary FROM emp;
```
```
SELECT dep_id FROM emp e1
WHERE e1.id  >= ALL (SELECT e2.id FROM employees e2 WHERE e1.dep_id = e2.dep_id) — using SUBQUERY
```
## 16. Find Maximum salary from each Department
```
SELECT dep, MAX(salary) FROM emp GROUP BY dep;
```
```
SELECT dep_id, MAX(salary) FROM emp e1
RIGHT OUTER JOIN emp e2 
ON (e1.dep_id = e2.id)
GROUP BY dep_id
```
```
SELECT dep_id, MAX(salary) FROM emp e1
LEFT OUTER JOIN emp e2 
ON (e2.id = e1.dep_id)
```
## 17. Find Dep-wise count of emp sorted by Dep’s count in ASC order
```
SELECT dep_id, COUNT(dep_id) FROM mep
GROUP BY dep_id
ORDER BY COUNT(dep_id);
```
```
SELECT D.name, COUNT(*) count FROM emp E
INNER JOIN  emp D
ON E.dep_id=D.id
GROUP BY D.name
ORDER BY count;
```
## 18. How to change the DataType of a column
```
ALTER TABLE <table_name>
MODIFY COLUMN mag_id BIGINT;
```
## 19. What is the Diff between UNIQUE KEY, PRIMARY KEY and FOREIGN KEY
__UNIQUE KEY:__
1. each column and row are unique
2. Uniquely identify when PRIMARY KEY is not present
3. Cannot accept Dupliactes
4. Multiple UNIQUE KEYS in a table
5. It can accept null value but only one null value per column 
6. We can modify unique key column value
7. It ensures the integrity of column or group of columns to store diff values in a table
```
CREATE TABLE emp(id INT NOT NULL,
				         name VARCHAR(255) NOT NULL,
				         salary VARCHAR(255),
				         UNIQUE(id));
```

```            
CREATE TABLE emp(id INT NOT NULL,
				          name VARCHAR(255) NOT NULL,
				          dep VARCHAR(255),
				          salary INT,
				          CONSTRAINT U_emp UNIQUE(id, name));
```
__PRINMARY KEY:__
1. Uniquely identify all table records
2. UNIQUE + NOT NULL
3. Only one PRIMARY KEY in a table
4. We cannot change or delete primary key  column values
5. Cannot allow NULL values
```
CREATE TABLE emp(id INT,
				          name VARCHAR(255) NOT NULL,
				          dep VARCHAR(255),
				          salary INT,
				          PRIMARY KEY (id));
```

```
CREATE TABLE emp(id INT,
				         name VARCHAR(255), NOT NULL,
				         dep VARCHAR(255),
				         salary INT,
				         CONSTRAINT pk_emp 
                 PRIMARY_KEY(id, name));
```
__FOREIGN KEY(referencing key):__
1. It is use to link one or more tables together
2. It is related to PRIMARY KEY of other table means foreign key field in one table refers to the primary key field of other table 
3. It can uniquely identify each row in another table 
4. Sometime it maintains the ACID properties 
```
CREATE TABLE Employee(id INT,
					            name VARCHAR(255) NOT NULL,
					            dep_id INT,
					            salary INT,
					            PRIMARY KEY(id),
					            FOREIGN KEY(dep_id) REFERENCES Department(id)
					            ON DELETE CASCADE.                      — permission for delete
					            ON UPDATE CASCADE);
```
## 20. Tell some clauses used with SELECT query 
FROM, WHERE, GROUP BY, HAVING ORDER BY etc
```
SELECT dep_id, COUNT(dep_id) FROM mep
WHERE dep_id !=2
GROUP BY dep_id
HAVING COUNT(id) > 2
ORDER BY dep_id DECS;
```
## 21. List Diff types of joins
Join: use to retrieve data from multiple tables into meaningful result set

1. INNER JOIN
```
SELECT * FROM table_A INNER JOIN table_B
```
2. CROSS JOIN
```
SELECT e.name, d.name FROM emp e CROSS JOIN dep d
```
3. SELF JOIN
```
SELECT e.emp_id, e.name, m.mgr_id, m.name
FROM emp e JOIN emp m
ON e.mgr_id = m.emp_id;
```

4. OUTER JOIN 
  i. LEFT OUTER JOIN
```
SELECT * FROM table_A a 
LEFT OUTER JOIN table_B b
ON a.id = b.id;
```

	ii. RIGHT OUTER JOIN
```
SELECT * FROM table_A a 
RIGHT OUTER JOIN table_B b
A.id = b.id;
```
	iii. FULL OUTER JOIN
```
SELECT * FROM table_A a 
FULL OUTER JOIN
table_B b 
ON a.id = b.id;
```
## 22. What are TRUNCATE, DELETE and DROP 
__TRUNCATE:__
1. Remove all rows
2. DDL
3. Cannot use WHERE 
4. Structure and schema will be there
5. Faster
6. Cannot ROLLBACK
7. TRUNCATE TABLE emp;
8. VIEWS exists



__DELETE:__
1. Remove single or multiple rows
2. DML
3. Can use WHERE
4. Structure and schema will be there
5. Slower
6. ROLLBACK
7. DELETE FROM emp WHERE id > 10;
8. VIEWS exists


__DROP:__
1. Remove object form database
2. DDL
3. Structure and schema will be remove
4. Cannot ROLLBACK
5. DROP TABLE emp;
6. VIEWS not exists

## 23. What are constraints in SQL
To specify limit on the type of data that can be stored in particular column in a table using constraints
```
NOT NULL, CHECK, DEFAULT, INDEX, UNIQUE, PRIMARY, FOREIGN 
```
## 24. Diff between WHERE and HAVING clause
__WHERE:__
1. Find records based on specific condition
2. It can be use without GROUP BY 
3. It can not contain aggregation function
4. Row level
5. Use with SELECT, UPDATE and DELETE

__HAVING:__
1. Filter records from group based specific condition
2. It can not be use without GROUP BY 
3. It contain aggregation function 
4. Column level
5. Use with SELECT only 

## 25. Diff between IN and BETWEEN operator
__BETWEEN:__ 
Take range of values(include 1st and last) it can be number, text, dates
```
SELECT * FROM emp
WHERE salary 
BETWEEN 25000 AND 30000;
```
__IN:__
Take specific values (multiple OR conditions)
```
SELECT * FROM emp
WHERE salary IN(23000, 25000, 27000);
```


## 26. What are Aggregation functions in SQL 
It is used to evaluate mathematical calculations and returns single value
```
COUNT, MAX, MIN, AVG, SUM 
```
## 27. What are the subsets of SQL (SQL Commands)
### DDL (data definition language)
Structure
```
DROP, RENAME, CREATE, ALTER, TRUNCATE
```
### DML (data manipulation language)
Write
```
INSERT, UPDATE, DELETE 
```
### DQL (data query language)
Read
```
SELECT
```
### DCL (data control language)
Permissions
```
GRANT, REVOKE
```
### TCL (transaction control language)
```
COMMITE, ROLLBACK, SAVEPOINT, SET TRANSACTION
```
## 28. Diff type of Relationships in SQL

1. One-to-One (user-password)
2. One-to-Many (class-student)
3. Many-to-One (student-class)
4. Many-to-Many (books-authors)
5. Self-Referencing (employee-manager)

## 29. What is RDBMS ? How it is diff from DBMS ?
__DBMS:__
1. Store data as file (hierarchical form or navigation form)
2. Normalization is not present in DBMS
3. It does not apply any security with regards to data manipulation
4. File system so there is no relationship between tables
5. It does not support distributed database
6. Ex. File system, xml

__RDBMS:__
1. Store data in a tabular form (tables have identifier called Primary key)
2. Normalization is present in RDBMS
3. It defines the integrity constraints for ACID property
4. Data in tabular form so relationship between data values store in table
5. It support distributed database
6. Ex. Mysql, postgre, SQL Server

## 30. Which function is used to return the remainder in a division operator in SQL ?
```
SELECT MOD(27, 5) AS “value”;
```
## 31. What is the diff between CHAR and VARCHAR datatype in SQL ?
__CHAR:__
1. To store character string of fixed length
2. If length of string is less then it is padded with extra space
3. Use when data value is of same length
4. Faster 

__VARCHAR:__
1. To store character string of variable length
2. If length of string is less then it is without padded with extra space
3. Use when data value is of diff length
4. Slower 

## 32. What are scalar functions in SQC ?
Inbuilt function, whatever input output will always single value
```
LENGTH(), UCASE(), LCASE(), MID(), ROUND(), NOW(), CONCAT(), RAND(), FORMAT()
```
## 33. What is UNION, UNION ALL, INTESECT, MINUS ?
__UNION:__
Combine the result of two or more select statements by removing duplicates. Columns and data types must be same in select statements
```
SELECT col FROM table1 UNION SELECT col FROM table2;
```
__UNION ALL:__
Same with union but not removing duplicates
```
SELECT col FROM table1 UNION ALL SELECT col FROM table2;
```
__INTERSECT:__
Returns the common records from two or more select statements
```
SELECT col FROM table1 INTERSECT SELECT col FROM table2;
```
__MINUS:__
Return the first query which is not found in 2nd query. Also it does not return the duplicate values
```
SELECT col FROM table1 MINUS SELECT col FROM table2;
```
