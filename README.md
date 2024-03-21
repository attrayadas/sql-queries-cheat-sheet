# SQL Query Cheat Sheet
This repository is a quick reference for SQL queries, covering fundamentals, Group By, Joins, Advanced SQL Commands, and Creating Databases and Tables.

# Table of Contents
1. [ SQL Statement Fundamentals ](#fundamentals)
2. [ GROUP BY Statements ](#groupby)
3. [ Joins ](#joins)
4. [ Advanced SQL Commands ](#advanced)
5. [ Creating Databases and Tables ](#create)
6. [ Conditional Expressions and Procedure ](#conditional)

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
* `CREATE TABLE` table_name(column_name `TYPE` column_constraint, column_name `TYPE` column_constraint, table_constraint table_constraint ) `INHERITS` existing_table_name;

eg:
* `CREATE TABLE` account(user_id `SERIAL PRIMARY KEY`, username `VARCHAR(50) UNIQUE NOT NULL`, password `VARCHAR(50) NOT NULL`, email `VARCHAR(250) UNIQUE NOT NULL`, created_on `TIMESTAMP NOT NULL`, last_login `TIMESTAMP`);
* `CREATE TABLE `job(job_id `SERIAL PRIMARY KEY`, job_name `VARCHAR(200) UNIQUE NOT NULL);`
* `CREATE TABLE` account_job(user_id `INTEGER REFERENCES` account(user_id), job_id `INTEGER REFERENCES` job(job_id), hire_date `TIMESTAMP`);

### INSERT:
General Syntax: <br/>
* `INSERT INTO` table (column1, column2, ...) `VALUES`(value1, value2, ...), (value1, value2, ...), ...;

INSERT allows you to add in rows to a table. Syntax for inserting values from another table:<br/>   
* `INSERT INTO` table (column1, column2,...) `SELECT` column1, column2, ... `FROM` another_table `WHERE` condition; 

eg:
* `INSERT INTO` account(username, password, email, created_on) `VALUES`('Attraya', 'password', 'attraya@gmail.com', CURRENT_TIMESTAMP);
* `INSERT INTO` job(job_name) `VALUES`('Software Engineer');
* `INSERT INTO` account_job(user_id, job_id, hire_date) `VALUES`(1, 1, `CURRENT_TIMESTAMP`);`

### UPDATE:
General Syntax: <br/>
* `UPDATE` table `SET` column1=value1, column2=value2,... `WHERE` condition;

eg:
* `UPDATE` account `SET` last_login = `CURRENT_TIMESTAMP WHERE` last_login `IS NULL`;
* `UPDATE` account `SET` last_login = created_on;
* `UPDATE` account `SET` last_login = `CURRENT_TIMESTAMP`;
* `UPDATE` TableA `SET` original_col = TableB.new_col `FROM` tableB `WHERE` tableA.id = TableB.id;
* `UPDATE` account_job `SET` hire_date = account.created_on `FROM` account `WHERE` account_job.user_id=account.user_id;

Returning affected rows: <br/>
* `UPDATE` account `SET` last_login = created_on `RETURNING` account_id, last_login;
* `UPDATE` account `SET` last_login = `CURRENT_TIMESTAMP RETURNING` email, created_on, last_login;

### DELETE:
General Syntax: <br/>
* `DELETE FROM` table `WHERE` row_id=1;

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

General Syntax: <br/>
* `ALTER TABLE` table_name action;

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

General Syntax: <br/> 
* `ALTER TABLE` table_name `DROP COLUMN` col_name;

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

General Syntax: <br/>                                                                                                 
* `CREATE TABLE` example(ex_id `SERIAL PRIMARY KEY`, age `SMALLINT CHECK` (age > 21), parent_age `SMALLINT CHECK`(parent_age > age));

eg:
* `CREATE TABLE` employees(emp_id `SERIAL PRIMARY KEY`, first_name `VARCHAR(50) NOT NULL`, last_name `VARCHAR(50) NOT NULL`, birthdate `DATE CHECK` (birthdate > '1900-01-01'), hire_date `DATE CHECK` (hire_date > birthdate), salary `INTEGER CHECK` (salary > 0));

