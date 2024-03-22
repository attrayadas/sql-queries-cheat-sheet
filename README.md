# SQL Query Cheat Sheet
This repository is a quick reference for SQL queries, covering fundamentals, Group By, Joins, Advanced SQL Commands, and Creating Databases and Tables.

# Table of Contents
1. [ SQL Statement Fundamentals ](#fundamentals)
2. [ GROUP BY Statements ](#groupby)
3. [ Joins ](#joins)
4. [ Advanced SQL Commands ](#advanced)
5. [ Creating Databases and Tables ](#create)
6. [ Conditional Expressions and Procedure ](#conditional)

<a name ="fundamentals"></a>
# 1. SQL Statement Fundamentals

### SELECT Statement
> `SELECT` column_name `FROM` table_name;
* `SELECT` * `FROM` actor;
* `SELECT` first_name, last_name `FROM` actor;

### SELECT DISTINCT
* `SELECT DISTINCT` choice `FROM` color_table;
* `SELECT DISTINCT`(release_year) `FROM` film;
* `SELECT DISTINCT`(rental_rate) `FROM` film;

### COUNT
* `SELECT COUNT`(amount) `FROM` payment;
* `SELECT COUNT`(*) `FROM` table;
* `SELECT COUNT(DISTINCT`(name)) `FROM` table;

### SELECT WHERE
> `SELECT` column1, column2 `FROM` table `WHERE` conditions;
* `SELECT` * `FROM` customer `WHERE` first_name = 'Jared';
* `SELECT` * `FROM` film `WHERE` rental_rate > 4;
* `SELECT` * `FROM` film `WHERE` rental_rate > 4 `AND` replacement_cost >= 19.99;
* `SELECT` title `FROM` film `WHERE` rental_rate > 4 `AND` replacement_cost >= 19.99 `AND` rating = 'R';
* `SELECT COUNT`(*) `FROM` film `WHERE` rental_rate > 4 `AND` replacement_cost >= 19.99 `AND` rating = 'R';
* `SELECT COUNT`(*) `FROM` film `WHERE` rating = 'R' `OR` rating = 'PG-13';
* `SELECT` * `FROM` film `WHERE` rating!= 'R';

### ORDER BY
> `SELECT` column1, column2 `FROM` table `ORDER BY` column1 `ASC/DESC`;
* `SELECT` * `FROM` customer `ORDER BY` first_name;
* `SELECT` * `FROM` customer `ORDER BY` first_name `DESC`;
* `SELECT` store_id, first_name, last_name `FROM` customer `ORDER BY` store_id, first_name;
* `SELECT` store_id, first_name, last_name `FROM` customer `ORDER BY` store_id `DESC`, first_name `ASC`;

### LIMIT: Useful in combination with ORDER BY and goes at the very end of a query request and is the last command to be executed
* `SELECT` * `FROM` payment `LIMIT` 1;
* `SELECT` * `FROM` payment `ORDER BY` payment_date `DESC LIMIT` 5;
* `SELECT` * `FROM` payment `WHERE` amount != 0.00 `ORDER BY` payment_date `DESC LIMIT` 5;

### BETWEEN: Used to match a value against a range of values
* `SELECT` * `FROM` payment `WHERE` amount `BETWEEN` 8 `AND` 9;
* `SELECT` `COUNT`(*) `FROM` payment `WHERE` amount `NOT BETWEEN` 8 `AND` 9;
* `SELECT` * `FROM` payment `WHERE` payment_date `BETWEEN` '2007-02-01' `AND` '2007-02-15';

### IN: Use the IN operator to create a condition that checks to see if a value is included in a list of multiple options
* `SELECT` * `FROM` payment `WHERE` amount `IN` (0.99, 1.98, 1.99);
* `SELECT` `COUNT`(*) `FROM` payment `WHERE` amount `NOT IN` (0.99, 1.98, 1.99);
* `SELECT` * `FROM` customer `WHERE` first_name `IN` ('John', 'Jake', 'Julie');

### LIKE and ILIKE: Pattern Matching
* LIKE: Allows us to perform pattern matching against string data with the use of wildcard characters:
  - *Percent %* : Matches any sequence of characters
  - *Underscore _* : Matches any single character
* LIKE is case-sensitive, ILIKE is case-insensitive
* `SELECT` * `FROM` customer `WHERE` first_name `LIKE` 'J%';
* `SELECT` * `FROM` customer `WHERE` first_name `LIKE` 'J%' `AND` last_name `LIKE` 'S%';
* `SELECT` * `FROM` customer `WHERE` first_name `ILIKE` 'j%' AND last_name `ILIKE` 's%';
* `SELECT` * `FROM` customer `WHERE` first_name `LIKE` '%er%';
* `SELECT` * `FROM` customer `WHERE` first_name `LIKE` '_her%';
* `SELECT` * `FROM` customer `WHERE` first_name `LIKE` 'A%' `AND` last_name `NOT LIKE` 'B%' `ORDER BY` last_name;

<a name ="groupby"></a>
# 2. GROUP BY Statements

* GROUP BY will allow us to aggregate data and apply functions to better understand how data is distributed per category.

### Aggregation Functions
* Common Aggregate Functions: *AVG(), COUNT(), MAX(), MIN(), SUM()*
* `SELECT MIN`(replacement_film) `FROM` film;
* `SELECT MAX`(replacement_film) `FROM` film;
* `SELECT MAX`(replacement_film), `MIN`(replacement_film) `FROM` film;
* `SELECT COUNT`(*) `FROM` film;
* `SELECT AVG`(replacement_cost) `FROM` film;
* `SELECT ROUND(AVG`(replacement_cost), 2) `FROM` film;
* `SELECT SUM`(replacement_cost) `FROM` film;

### GROUP BY
General Syntax:
* The GROUP BY clause must appear right after a FROM or WHERE statement.<br/>
  >`SELECT` category_col, `AGG`(data_col) `FROM` table `GROUP BY` category_col;</br>
  >`SELECT` category_col, `AGG`(data_col) `FROM` table `WHERE` category_col!='A' `GROUP BY` category_col;
 
* In the SELECT statement, columns must either have an aggregate function or be in the GROUP BY call.<br/>
  >`SELECT` company, division, `SUM`(sales) `FROM` finance_table `GROUP BY` company, division;

* If you want to sort results based on the aggregate, make sure to reference the entire function<br/>
  >`SELECT` company, `SUM`(sales) `FROM` finance_table `GROUP BY` company `ORDER BY SUM`(sales);

* `SELECT` customer_id `FROM` payment `GROUP BY` customer_id;
* `SELECT` customer_id, `SUM`(amount) `FROM` payment `GROUP BY` customer_id `ORDER BY` `SUM`(amount);
* `SELECT` customer_id, `COUNT`(amount) `FROM` payment `GROUP BY` customer_id `ORDER BY COUNT`(amount) `DESC`;
* `SELECT` customer_id, staff_id, `SUM`(amount) `FROM` payment `GROUP BY` staff_id, customer_id;
* `SELECT` `DATE`(payment_date), `SUM`(amount) `FROM` payment `GROUP BY` `DATE`(payment_date) `ORDER BY SUM`(amount);
* `SELECT` rating, `AVG`(replacement_cost) `FROM` film `GROUP BY` rating;

### HAVING: Allows us to filter after an aggregation has already taken place
* HAVING allows us to use the aggregate result as a filter along with a GROUP BY
* `SELECT` company, `SUM`(sales) `FROM` finance_table `WHERE` company!='Google' `GROUP BY` company `HAVING` `SUM`(sales)>1000
* `SELECT` customer_id, `SUM`(amount) `FROM` payment `GROUP BY` customer_id `HAVING SUM`(amount) > 100;
* `SELECT` store_id, `COUNT`(* ) `FROM` customer `GROUP BY` store_id `HAVING COUNT`(* ) > 300;


<a name ="joins"></a>
# 3. Joins: Allow us to combine information from multiple tables

Resources:<br/>
https://blog.codinghorror.com/a-visual-explanation-of-sql-joins/<br/>
https://en.wikipedia.org/wiki/Join_(SQL)<br/>
https://medium.com/@josemarcialportilla/review-of-sql-joins-ac5463dc71c9#.ayjcuatvj

* Different kinds of Joins:
  * INNER JOIN
  * OUTER JOIN
    * FULL OUTER JOIN
      * FULL OUTER JOIN with WHERE
    * LEFT OUTER JOIN
      * LEFT OUTER JOIN with WHERE
    * RIGHT OUTER JOIN
      * RIGHT OUTER JOIN with WHERE
  * UNION

***INNER JOIN---*** grabs things that are common to both tables <br/>
***FULL OUTER JOIN ---*** grabs everything from both tables <br/>
***FULL OUTER JOIN with WHERE ---*** grabs only those rows that are exclusive to table A and rows exclusive to table B<br/>
***LEFT OUTER JOIN ---*** grabs everything from left-hand table. (that includes rows unique to left table & rows that are in match with right table) we fill in the right hand table information where there is a match on the column defined.<br/>
***LEFT OUTER JOIN with WHERE ---*** grabs only rows that are unique to left-hand table.<br/>
***RIGHT OUTER JOIN---*** grabs everything from right-hand table.  we can also achieve this by using LEFT JOIN and then switch the order of tables based on our need.<br/>

### AS

* `SELECT` column `AS` new_name `FROM` table;
* `SELECT` SUM(column) `AS` new_name `FROM` table;
* `SELECT` amount `AS` rental_price `FROM` payment;
* `SELECT` SUM(amount) `AS` net_revenue `FROM` payment;
* `SELECT` COUNT(amount) `AS` num_transactions `FROM` payment;
* `SELECT` customer_id, `SUM`(amount) `AS` total_spent `FROM` payment `GROUP BY` customer_id;
* `SELECT` customer_id, `SUM`(amount) `AS` total_spent `FROM` payment `GROUP BY` customer_id `HAVING` total_spent > 100; // error as alias gets assigned at the very end , so can't be used in WHERE clause
* `SELECT` customer_id, `SUM`(amount) `AS` total_spent `FROM` payment `GROUP BY` customer_id `HAVING` `SUM`(amount) > 100;

### INNER JOIN: grabs things that are common to both tables (Symmetrical)

> `SELECT` * `FROM` TableA `INNER JOIN` TableB `ON` TableA.col_match = TableB.col_match;
* `SELECT` reg_id, Logins.name, log_id `FROM` registrations `INNER JOIN` logins `ON` registrations.name=logins.name;
* `SELECT` payment_id, payment.customer_id, first_name `FROM` payment `INNER JOIN` customer `ON` payment.customer_id = customer.customer_id;
* `SELECT` email `FROM` customer `INNER JOIN` address `ON` address.address_id = customer.address_id `WHERE` district = 'California';
* `SELECT` title, first_name , last_name `FROM` actor `INNER JOIN` film_actor `ON` actor.actor_id = film_actor.actor_id `INNER JOIN` film `ON` film_actor.film_id = film.film_id `WHERE` first_name ='Nick' `AND` last_name ='Wahlberg';

### FULL OUTER JOIN: grabs everything from both tables. (Symmetrical)
> `SELECT` * `FROM` TableA `FULL OUTER JOIN` TableB `ON` TableA.col_match = TableB.col_match;
* `SELECT` * `FROM` Registrations `FULL OUTER JOIN` Logins `ON` Registrations.name = Logins.name;

### FULL OUTER JOIN with WHERE: grabs only those rows that are exclusive to table A and rows exclusive to table B (opposite of inner join)
> `SELECT` * `FROM` TableA `FULL OUTER JOIN` TableB `ON` TableA.col_match = TableB.col_match `WHERE` TableA.id IS NULL OR TableB.id `IS NULL`;
* `SELECT` * `FROM` Registrations `FULL OUTER JOIN` Logins `ON` Registrations.name = Logins.name `WHERE` Registrations.reg_id `IS NULL` `OR` Logins.log_id `IS NULL`;
* `SELECT` * `FROM` customer `FULL OUTER JOIN` payment `ON` customer.customer_id = payment.customer_id `WHERE` customer.customer_id `IS NULL OR` payment.payment_id `IS NULL`;

### LEFT OUTER JOIN: Results in the set of records that are in the left table, if there is no match with the right table, the results are null
> `SELECT` * FROM TableA `LEFT OUTER JOIN` TableB `ON` TableA.col_match = TableB.col_match;
* `SELECT` film.film_id, title, inventory_id, store_id `FROM` film `LEFT OUTER JOIN` inventory `ON` inventory.film_id = film.film_id;

### LEFT OUTER JOIN With WHERE: grabs only rows that are unique to left-hand table.
> `SELECT` * `FROM` TableA `LEFT OUTER JOIN` TableB `ON` TableA.col_match = TableB.col_match `WHERE` TableB.id `IS NULL`;
* `SELECT` film.film_id, title, inventory_id, store_id `FROM` film `LEFT OUTER JOIN` inventory `ON` inventory.film_id = film.film_id `WHERE` inventory.film_id `IS NULL`;

### RIGHT OUTER JOIN: grabs everything from right-hand table. we can also achieve this by using LEFT JOIN and then switch the order of tables based on our need.
>`SELECT` * `FROM` TableA `RIGHT OUTER JOIN` TableB `ON` TableA.col_match = TableB.col_match;

### RIGHT OUTER JOIN with WHERE:
> `SELECT` * `FROM` TableA `RIGHT OUTER JOIN` TableB `ON` TableA.col_match = TableB.col_match `WHERE` TableA.id `IS NULL`;

### UNION: Used to combine the result-set of two or more SELECT statements (concatenate two results together)
>`SELECT` column_name(s) `FROM` table1 `UNION` `SELECT` column_name(s) `FROM` table2;
* `SELECT` * `FROM` Sales2021_Q1 `UNION SELECT` * `FROM` Sales2021_Q2 `ORDER BY` name;


<a name="create"></a>
# 5. Creating Databases and Tables

### Datatypes:
* Boolean (true, false), Character (char, varchar, text), Numeric (integer, floating-point), Temporal (date, time, timestamp, interval), UUID, Arrays, JSON, Hstore key-value pair, and special types.

### Primary Keys:
* Column or a group of columns used to identify a row uniquely in a table.
* It's also important since they allow us to easily discern what columns should be used for joining tables together

### Foreign Keys:
* Field or group of fields in a table that uniquely identifies a row in another table.
* A foreign key is defined in a table that references to the primary key of the other table.

### Constraints:
- The rules enforced on data columns on table
- Used to prevent invalid data from being entered into the database.
- Ensures the accuracy and reliability of the data in the database.
- Two main categories:
  1. *Column Constraints:* Constrains the data in a column to adhere to certain conditions
  2. *Table Constraints:* Applied to the entire table rather than to an individual column.
- Most Common Constraints used:
  1. *NOT NULL* Constraint: Ensures that a column cannot have NULL value.
  2. *UNIQUE* Constraint: Ensures that all values in a column are different.
  3. *PRIMARY Key*: Uniquely identifies each row/record in a database table.
  4. *FOREIGN Key*: Constraints data based on columns in other tables.
  5. *CHECK* Constraint: Ensures that all values in a column satisfy certain conditions.
  6. *EXCLUSION* Constraint: Ensures that if any two rows are compared on the specified column or expression using the specified operator, not all of these comparisons will return TRUE.
- Table Constraints:
  1. *CHECK(condition)*: to check  a condition when inserting or updating data.
  2. *REFERENCES*: to constrain the values stored in the column that must exist in a column in another table.
  3. *UNIQUE(column_list)*: forces the values stored in the columns listed inside the parentheses to be unique.
  4. *PRIMARY KEY(column_list)*: Allows you to define the primary key that consists of multiple columns.

### CREATE Table:
> `CREATE TABLE` table_name(column_name `TYPE` column_constraint, column_name `TYPE` column_constraint, table_constraint table_constraint ) `INHERITS` existing_table_name;
* `CREATE TABLE` account(user_id `SERIAL PRIMARY KEY`, username `VARCHAR(50) UNIQUE NOT NULL`, password `VARCHAR(50) NOT NULL`, email `VARCHAR(250) UNIQUE NOT NULL`, created_on `TIMESTAMP NOT NULL`, last_login `TIMESTAMP`);
* `CREATE TABLE `job(job_id `SERIAL PRIMARY KEY`, job_name `VARCHAR(200) UNIQUE NOT NULL);`
* `CREATE TABLE` account_job(user_id `INTEGER REFERENCES` account(user_id), job_id `INTEGER REFERENCES` job(job_id), hire_date `TIMESTAMP`);

### INSERT:
> `INSERT INTO` table (column1, column2, ...) `VALUES`(value1, value2, ...), (value1, value2, ...), ...;

* INSERT allows you to add in rows to a table. Syntax for inserting values from another table:<br/>
* `INSERT INTO` table (column1, column2,...) `SELECT` column1, column2, ... `FROM` another_table `WHERE` condition;


* `INSERT INTO` account(username, password, email, created_on) `VALUES`('Attraya', 'password', 'attraya@gmail.com', CURRENT_TIMESTAMP);
* `INSERT INTO` job(job_name) `VALUES`('Software Engineer');
* `INSERT INTO` account_job(user_id, job_id, hire_date) `VALUES`(1, 1, `CURRENT_TIMESTAMP`);`

### UPDATE:
> `UPDATE` table `SET` column1=value1, column2=value2,... `WHERE` condition;

* `UPDATE` account `SET` last_login = `CURRENT_TIMESTAMP WHERE` last_login `IS NULL`;
* `UPDATE` account `SET` last_login = created_on;
* `UPDATE` account `SET` last_login = `CURRENT_TIMESTAMP`;
* `UPDATE` TableA `SET` original_col = TableB.new_col `FROM` tableB `WHERE` tableA.id = TableB.id;
* `UPDATE` account_job `SET` hire_date = account.created_on `FROM` account `WHERE` account_job.user_id=account.user_id;

> Returning affected rows: <br/>
* `UPDATE` account `SET` last_login = created_on `RETURNING` account_id, last_login;
* `UPDATE` account `SET` last_login = `CURRENT_TIMESTAMP RETURNING` email, created_on, last_login;

### DELETE:
> `DELETE FROM` table `WHERE` row_id=1;

We can delete rows based on their presence in other tables: <br/>
* `DELETE FROM` tableA `USING` tableB `WHERE` tableA.id=TableB.id;

Delete all rows from a table: <br/>
* `DELETE FROM` table;

### ALTER Table:
The ALTER clause allows for changes to an existing table structure, such as:
- Adding, dropping, or renaming columns
- Changing a column's data type
- Set DEFAULT values for a column
- Add CHECK constraints
- Rename table

> `ALTER TABLE` table_name action;

Adding Columns: <br/>
* `ALTER TABLE` table_name `ADD COLUMN` new_col `TYPE`;

Removing Columns: <br/>
* `ALTER TABLE` table_name `DROP COLUMN` col_name;

Alter constraints: <br/>
* `ALTER TABLE` table_name `ALTER COLUMN` col_name `SET DEFAULT` value; <br/>
* `ALTER TABLE` table_name `ALTER COLUMN` col_name `SET NOT NULL`; <br/>
* `ALTER TABLE` table_name `ALTER COLUMN` col_name `ADD CONSTRAINT` constraint_name;

eg:
* `ALTER TABLE` information `RENAME TO` new_info;
* `ALTER TABLE` new_info `RENAME COLUMN` person To people;
* `ALTER TABLE` new_info `ALTER COLUMN` people `DROP NOT NULL`;

### DROP Table:
DROP allows for the complete removal of a column in a table

> `ALTER TABLE` table_name `DROP COLUMN` col_name;

Remove all dependencies: </br>
* `ALTER TABLE` table_name `DROP COLUMN` col_name `CASCADE`;

Check for existence to avoid error:<br/>
* `DROP COLUMN IF EXISTS` col_name;

Drop multiple columns:<br/>
* `ALTER TABLE` table_name `DROP COLUMN` col_one, `DROP COLUMN` col_two;

eg:
* `ALTER TABLE` new_info `DROP COLUMN people;`
* `ALTER TABLE` new_info `DROP COLUMN IF EXISTS` people;

### CHECK Constraint:
The CHECK constraint allows us to create more customized constraints that adhere to a certain condition.
eg: make sure all inserted integer values fall below a certain threshold.

>`CREATE TABLE` example(ex_id `SERIAL PRIMARY KEY`, age `SMALLINT CHECK` (age > 21), parent_age `SMALLINT CHECK`(parent_age > age));


* `CREATE TABLE` employees(emp_id `SERIAL PRIMARY KEY`, first_name `VARCHAR(50) NOT NULL`, last_name `VARCHAR(50) NOT NULL`, birthdate `DATE CHECK` (birthdate > '1900-01-01'), hire_date `DATE CHECK` (hire_date > birthdate), salary `INTEGER CHECK` (salary > 0));

<img src="assets/SQL Cheat Sheet.png" alt="sql cheat sheet" style="width: 100%;">