# Complete SQL Mastery Guide: MySQL, SQLite & PostgreSQL

## Table of Contents

1. [Introduction to SQL](#introduction-to-sql)
2. [Database Basics](#database-basics)
3. [Data Types](#data-types)
4. [Creating Databases and Tables](#creating-databases-and-tables)
5. [CRUD Operations](#crud-operations)
6. [Filtering and Sorting](#filtering-and-sorting)
7. [Joins](#joins)
8. [Aggregate Functions](#aggregate-functions)
9. [Subqueries](#subqueries)
10. [Views](#views)
11. [Indexes](#indexes)
12. [Transactions](#transactions)
13. [Advanced Topics](#advanced-topics)
14. [Constraints](#constraints)
15. [Stored Procedures and Functions](#stored-procedures-and-functions)
16. [Triggers](#triggers)
17. [Performance Optimization](#performance-optimization)
18. [Security Best Practices](#security-best-practices)
19. [Database-Specific Features](#database-specific-features)

---

## Introduction to SQL

SQL (Structured Query Language) is a standard language for managing and manipulating relational databases. It allows you to create, read, update, and delete data.

**Key Concepts:**

- **Database**: A collection of organized data
- **Table**: A collection of related data organized in rows and columns
- **Row/Record**: A single entry in a table
- **Column/Field**: A specific attribute of data in a table

---

## Database Basics

### Connecting to Databases

**MySQL:**

```sql
mysql -u username -p
-- Enter password when prompted
```

**PostgreSQL:**

```sql
psql -U username -d database_name
```

**SQLite:**

```sql
sqlite3 database_name.db
```

### Creating a Database

**MySQL & PostgreSQL:**

```sql
CREATE DATABASE company;
```

**Explanation:** Creates a new database named "company". In SQLite, the database is created when you open a file.

### Listing Databases

**MySQL:**

```sql
SHOW DATABASES;
```

**PostgreSQL:**

```sql
\l
-- or
SELECT datname FROM pg_database;
```

**SQLite:**

```sql
.databases
```

### Using a Database

**MySQL:**

```sql
USE company;
```

**PostgreSQL:**

```sql
\c company
```

**Explanation:** Switches context to the specified database. SQLite doesn't need this as you're already in the database file.

### Dropping a Database

```sql
DROP DATABASE company;
```

**Explanation:** Permanently deletes the database and all its data. Use with caution!

---

## Data Types

### Common Data Types Across Systems

**Numeric Types:**

- `INT` / `INTEGER`: Whole numbers
- `DECIMAL(p,s)` / `NUMERIC(p,s)`: Fixed-point numbers (p=precision, s=scale)
- `FLOAT` / `REAL`: Floating-point numbers
- `DOUBLE`: Double-precision floating-point

**String Types:**

- `CHAR(n)`: Fixed-length string
- `VARCHAR(n)`: Variable-length string
- `TEXT`: Large text data

**Date and Time:**

- `DATE`: Date (YYYY-MM-DD)
- `TIME`: Time (HH:MM:SS)
- `DATETIME` (MySQL) / `TIMESTAMP` (PostgreSQL): Date and time combined

**Boolean:**

- `BOOLEAN` / `BOOL`: True/False values

**Example:**

```sql
-- Different data types in use
CREATE TABLE products (
    product_id INT,
    product_name VARCHAR(100),
    price DECIMAL(10, 2),
    in_stock BOOLEAN,
    created_at TIMESTAMP
);
```

---

## Creating Databases and Tables

### Creating a Table

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    hire_date DATE,
    salary DECIMAL(10, 2),
    department_id INT
);
```

**Explanation:**

- `PRIMARY KEY`: Uniquely identifies each row
- `NOT NULL`: Field cannot be empty
- `UNIQUE`: All values must be different
- `DECIMAL(10, 2)`: Up to 10 digits total, 2 after decimal point

### Creating Table with Auto-Increment

**MySQL:**

```sql
CREATE TABLE employees (
    employee_id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50)
);
```

**PostgreSQL:**

```sql
CREATE TABLE employees (
    employee_id SERIAL PRIMARY KEY,
    first_name VARCHAR(50)
);
```

**SQLite:**

```sql
CREATE TABLE employees (
    employee_id INTEGER PRIMARY KEY AUTOINCREMENT,
    first_name TEXT
);
```

**Explanation:** Auto-increment automatically generates unique IDs for new rows.

### Creating Table with Foreign Key

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    order_date DATE,
    customer_id INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
```

**Explanation:** Foreign key creates a relationship between tables, ensuring customer_id exists in the customers table.

### Viewing Table Structure

**MySQL:**

```sql
DESCRIBE employees;
-- or
SHOW COLUMNS FROM employees;
```

**PostgreSQL:**

```sql
\d employees
-- or
SELECT column_name, data_type FROM information_schema.columns
WHERE table_name = 'employees';
```

**SQLite:**

```sql
.schema employees
-- or
PRAGMA table_info(employees);
```

### Altering Tables

**Add a Column:**

```sql
ALTER TABLE employees ADD COLUMN phone VARCHAR(20);
```

**Modify a Column (MySQL):**

```sql
ALTER TABLE employees MODIFY COLUMN salary DECIMAL(12, 2);
```

**Modify a Column (PostgreSQL):**

```sql
ALTER TABLE employees ALTER COLUMN salary TYPE DECIMAL(12, 2);
```

**Drop a Column:**

```sql
ALTER TABLE employees DROP COLUMN phone;
```

**Rename a Table:**

```sql
ALTER TABLE employees RENAME TO staff;
```

### Dropping Tables

```sql
DROP TABLE employees;
```

**Explanation:** Permanently deletes the table and all its data.

---

## CRUD Operations

### INSERT - Creating Data

**Insert Single Row:**

```sql
INSERT INTO employees (employee_id, first_name, last_name, email, hire_date, salary)
VALUES (1, 'John', 'Doe', 'john.doe@company.com', '2023-01-15', 55000.00);
```

**Insert Multiple Rows:**

```sql
INSERT INTO employees (employee_id, first_name, last_name, email, hire_date, salary)
VALUES
    (2, 'Jane', 'Smith', 'jane.smith@company.com', '2023-02-20', 60000.00),
    (3, 'Mike', 'Johnson', 'mike.j@company.com', '2023-03-10', 52000.00),
    (4, 'Sarah', 'Williams', 'sarah.w@company.com', '2023-04-05', 58000.00);
```

**Insert from Another Table:**

```sql
INSERT INTO archived_employees
SELECT * FROM employees WHERE hire_date < '2020-01-01';
```

**Explanation:** Copies data from employees to archived_employees for records before 2020.

### SELECT - Reading Data

**Select All Columns:**

```sql
SELECT * FROM employees;
```

**Select Specific Columns:**

```sql
SELECT first_name, last_name, salary FROM employees;
```

**Select with Alias:**

```sql
SELECT first_name AS "First Name",
       last_name AS "Last Name",
       salary * 12 AS "Annual Salary"
FROM employees;
```

**Explanation:** `AS` creates a temporary name for columns. Calculated fields like `salary * 12` compute values on-the-fly.

**Select Distinct Values:**

```sql
SELECT DISTINCT department_id FROM employees;
```

**Explanation:** Returns only unique department IDs, eliminating duplicates.

### UPDATE - Modifying Data

**Update Single Record:**

```sql
UPDATE employees
SET salary = 60000.00
WHERE employee_id = 1;
```

**Update Multiple Records:**

```sql
UPDATE employees
SET salary = salary * 1.10
WHERE department_id = 5;
```

**Explanation:** Gives a 10% raise to all employees in department 5.

**Update Multiple Columns:**

```sql
UPDATE employees
SET salary = 65000.00,
    department_id = 3
WHERE employee_id = 2;
```

**Warning:** Always use WHERE clause! Without it, all rows will be updated.

### DELETE - Removing Data

**Delete Specific Records:**

```sql
DELETE FROM employees WHERE employee_id = 1;
```

**Delete with Condition:**

```sql
DELETE FROM employees WHERE hire_date < '2020-01-01';
```

**Delete All Records (keep table structure):**

```sql
DELETE FROM employees;
```

**Truncate Table (faster, resets auto-increment):**

```sql
TRUNCATE TABLE employees;
```

**Warning:** Always use WHERE clause! Without it, all rows will be deleted.

---

## Filtering and Sorting

### WHERE Clause

**Basic Comparison:**

```sql
SELECT * FROM employees WHERE salary > 55000;
```

**Multiple Conditions (AND):**

```sql
SELECT * FROM employees
WHERE salary > 50000 AND department_id = 2;
```

**Multiple Conditions (OR):**

```sql
SELECT * FROM employees
WHERE department_id = 2 OR department_id = 3;
```

**Combining AND/OR:**

```sql
SELECT * FROM employees
WHERE (department_id = 2 OR department_id = 3) AND salary > 50000;
```

**Explanation:** Parentheses control the order of evaluation, just like in math.

### Comparison Operators

```sql
-- Equal to
SELECT * FROM employees WHERE department_id = 2;

-- Not equal to
SELECT * FROM employees WHERE department_id != 2;
-- or
SELECT * FROM employees WHERE department_id <> 2;

-- Greater than
SELECT * FROM employees WHERE salary > 50000;

-- Less than or equal to
SELECT * FROM employees WHERE salary <= 60000;

-- Between range
SELECT * FROM employees WHERE salary BETWEEN 50000 AND 60000;

-- In list
SELECT * FROM employees WHERE department_id IN (1, 2, 3);

-- Not in list
SELECT * FROM employees WHERE department_id NOT IN (4, 5);
```

### Pattern Matching with LIKE

```sql
-- Starts with 'J'
SELECT * FROM employees WHERE first_name LIKE 'J%';

-- Ends with 'son'
SELECT * FROM employees WHERE last_name LIKE '%son';

-- Contains 'oh'
SELECT * FROM employees WHERE first_name LIKE '%oh%';

-- Second letter is 'a'
SELECT * FROM employees WHERE first_name LIKE '_a%';
```

**Explanation:**

- `%` matches any sequence of characters
- `_` matches exactly one character

### NULL Values

```sql
-- Find NULL values
SELECT * FROM employees WHERE email IS NULL;

-- Find non-NULL values
SELECT * FROM employees WHERE email IS NOT NULL;
```

**Important:** Never use `= NULL` or `!= NULL`, always use `IS NULL` or `IS NOT NULL`.

### ORDER BY - Sorting Results

**Ascending Order (default):**

```sql
SELECT * FROM employees ORDER BY last_name;
-- or explicitly
SELECT * FROM employees ORDER BY last_name ASC;
```

**Descending Order:**

```sql
SELECT * FROM employees ORDER BY salary DESC;
```

**Multiple Columns:**

```sql
SELECT * FROM employees
ORDER BY department_id ASC, salary DESC;
```

**Explanation:** Sorts by department first (ascending), then by salary within each department (descending).

**Order by Calculated Field:**

```sql
SELECT first_name, last_name, salary * 12 AS annual_salary
FROM employees
ORDER BY annual_salary DESC;
```

### LIMIT - Restricting Results

**MySQL & PostgreSQL:**

```sql
SELECT * FROM employees ORDER BY salary DESC LIMIT 5;
```

**SQLite:**

```sql
SELECT * FROM employees ORDER BY salary DESC LIMIT 5;
```

**PostgreSQL with OFFSET:**

```sql
SELECT * FROM employees ORDER BY salary DESC LIMIT 5 OFFSET 10;
```

**Explanation:** Returns 5 rows starting from row 11 (skipping first 10). Useful for pagination.

**MySQL Alternative:**

```sql
SELECT * FROM employees ORDER BY salary DESC LIMIT 10, 5;
```

---

## Joins

Joins combine rows from two or more tables based on related columns.

### Sample Tables for Join Examples

```sql
-- Departments table
CREATE TABLE departments (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(50)
);

INSERT INTO departments VALUES
(1, 'Engineering'),
(2, 'Sales'),
(3, 'Marketing'),
(4, 'HR');

-- Employees table
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    emp_name VARCHAR(50),
    dept_id INT,
    salary DECIMAL(10, 2)
);

INSERT INTO employees VALUES
(1, 'Alice', 1, 75000),
(2, 'Bob', 2, 65000),
(3, 'Charlie', 1, 70000),
(4, 'Diana', NULL, 60000),
(5, 'Eve', 2, 68000);
```

### INNER JOIN

Returns only matching rows from both tables.

```sql
SELECT employees.emp_name, departments.dept_name, employees.salary
FROM employees
INNER JOIN departments ON employees.dept_id = departments.dept_id;
```

**Result:**

```
emp_name | dept_name    | salary
---------|--------------|--------
Alice    | Engineering  | 75000
Bob      | Sales        | 65000
Charlie  | Engineering  | 70000
Eve      | Sales        | 68000
```

**Explanation:** Diana is excluded because she has no department. Department 4 (HR) is excluded because it has no employees.

**Using Table Aliases:**

```sql
SELECT e.emp_name, d.dept_name, e.salary
FROM employees e
INNER JOIN departments d ON e.dept_id = d.dept_id;
```

### LEFT JOIN (LEFT OUTER JOIN)

Returns all rows from left table and matching rows from right table. NULL for non-matching right table rows.

```sql
SELECT e.emp_name, d.dept_name, e.salary
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id;
```

**Result:**

```
emp_name | dept_name    | salary
---------|--------------|--------
Alice    | Engineering  | 75000
Bob      | Sales        | 65000
Charlie  | Engineering  | 70000
Diana    | NULL         | 60000
Eve      | Sales        | 68000
```

**Explanation:** All employees are shown, including Diana who has no department.

### RIGHT JOIN (RIGHT OUTER JOIN)

Returns all rows from right table and matching rows from left table.

```sql
SELECT e.emp_name, d.dept_name
FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.dept_id;
```

**Result:**

```
emp_name | dept_name
---------|-------------
Alice    | Engineering
Charlie  | Engineering
Bob      | Sales
Eve      | Sales
NULL     | Marketing
NULL     | HR
```

**Explanation:** All departments are shown, including those without employees.

**Note:** SQLite doesn't support RIGHT JOIN. Use LEFT JOIN with reversed table order instead.

### FULL OUTER JOIN

Returns all rows when there's a match in either table.

**PostgreSQL:**

```sql
SELECT e.emp_name, d.dept_name
FROM employees e
FULL OUTER JOIN departments d ON e.dept_id = d.dept_id;
```

**Note:** MySQL and SQLite don't support FULL OUTER JOIN directly. You can simulate it with UNION:

```sql
SELECT e.emp_name, d.dept_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id
UNION
SELECT e.emp_name, d.dept_name
FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.dept_id;
```

### CROSS JOIN

Returns Cartesian product (all possible combinations).

```sql
SELECT e.emp_name, d.dept_name
FROM employees e
CROSS JOIN departments d;
```

**Explanation:** If employees has 5 rows and departments has 4 rows, result will have 20 rows (5 × 4).

### SELF JOIN

Joins a table to itself.

```sql
-- Find employees with same salary
SELECT e1.emp_name AS employee1, e2.emp_name AS employee2, e1.salary
FROM employees e1
INNER JOIN employees e2 ON e1.salary = e2.salary AND e1.emp_id < e2.emp_id;
```

**Explanation:** The `e1.emp_id < e2.emp_id` condition prevents duplicate pairs and self-matching.

### Multiple Joins

```sql
CREATE TABLE projects (
    project_id INT PRIMARY KEY,
    project_name VARCHAR(50),
    emp_id INT
);

SELECT e.emp_name, d.dept_name, p.project_name
FROM employees e
INNER JOIN departments d ON e.dept_id = d.dept_id
INNER JOIN projects p ON e.emp_id = p.emp_id;
```

**Explanation:** Joins three tables to show employee, their department, and their project.

---

## Aggregate Functions

Aggregate functions perform calculations on multiple rows and return a single value.

### COUNT

```sql
-- Count all rows
SELECT COUNT(*) FROM employees;

-- Count non-NULL values
SELECT COUNT(email) FROM employees;

-- Count distinct values
SELECT COUNT(DISTINCT department_id) FROM employees;
```

### SUM

```sql
-- Total of all salaries
SELECT SUM(salary) AS total_payroll FROM employees;

-- Sum with condition
SELECT SUM(salary) FROM employees WHERE department_id = 2;
```

### AVG

```sql
-- Average salary
SELECT AVG(salary) AS average_salary FROM employees;

-- Average by department
SELECT department_id, AVG(salary) AS avg_dept_salary
FROM employees
GROUP BY department_id;
```

### MIN and MAX

```sql
-- Lowest and highest salaries
SELECT MIN(salary) AS lowest_salary,
       MAX(salary) AS highest_salary
FROM employees;

-- Find employee with highest salary
SELECT first_name, last_name, salary
FROM employees
WHERE salary = (SELECT MAX(salary) FROM employees);
```

### GROUP BY

Groups rows with same values into summary rows.

```sql
-- Count employees per department
SELECT department_id, COUNT(*) AS employee_count
FROM employees
GROUP BY department_id;

-- Average salary per department
SELECT department_id, AVG(salary) AS avg_salary
FROM employees
GROUP BY department_id
ORDER BY avg_salary DESC;
```

**Multiple Columns:**

```sql
-- Count by department and year
SELECT department_id,
       YEAR(hire_date) AS hire_year,
       COUNT(*) AS hires
FROM employees
GROUP BY department_id, YEAR(hire_date);
```

### HAVING

Filters groups (used with GROUP BY). WHERE filters rows before grouping, HAVING filters after.

```sql
-- Departments with more than 5 employees
SELECT department_id, COUNT(*) AS employee_count
FROM employees
GROUP BY department_id
HAVING COUNT(*) > 5;

-- Departments with average salary > 55000
SELECT department_id, AVG(salary) AS avg_salary
FROM employees
GROUP BY department_id
HAVING AVG(salary) > 55000;
```

**Combining WHERE and HAVING:**

```sql
-- Average salary > 55000 for employees hired after 2020
SELECT department_id, AVG(salary) AS avg_salary
FROM employees
WHERE hire_date > '2020-01-01'
GROUP BY department_id
HAVING AVG(salary) > 55000;
```

**Explanation:** WHERE filters individual employees, then GROUP BY groups them, then HAVING filters the groups.

---

## Subqueries

A subquery is a query nested inside another query.

### Subquery in WHERE Clause

```sql
-- Employees earning more than average
SELECT first_name, last_name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

**Explanation:** Inner query calculates average, outer query uses that value.

### Subquery with IN

```sql
-- Employees in departments located in New York
SELECT first_name, last_name
FROM employees
WHERE department_id IN (
    SELECT dept_id
    FROM departments
    WHERE location = 'New York'
);
```

### Subquery with EXISTS

```sql
-- Departments that have employees
SELECT dept_name
FROM departments d
WHERE EXISTS (
    SELECT 1
    FROM employees e
    WHERE e.department_id = d.dept_id
);
```

**Explanation:** EXISTS returns true if subquery returns any rows.

### Subquery in SELECT

```sql
-- Show employee salary compared to department average
SELECT first_name,
       last_name,
       salary,
       (SELECT AVG(salary)
        FROM employees e2
        WHERE e2.department_id = e1.department_id) AS dept_avg
FROM employees e1;
```

### Subquery in FROM (Derived Table)

```sql
-- Average of department averages
SELECT AVG(dept_avg_salary) AS company_wide_avg
FROM (
    SELECT department_id, AVG(salary) AS dept_avg_salary
    FROM employees
    GROUP BY department_id
) AS dept_averages;
```

**Explanation:** Inner query creates a temporary result set that outer query treats as a table.

### Correlated Subquery

```sql
-- Employees earning more than their department's average
SELECT e1.first_name, e1.last_name, e1.salary
FROM employees e1
WHERE e1.salary > (
    SELECT AVG(e2.salary)
    FROM employees e2
    WHERE e2.department_id = e1.department_id
);
```

**Explanation:** Subquery references outer query's table. Executes once for each row in outer query.

---

## Views

A view is a virtual table based on a query. It doesn't store data but provides a way to simplify complex queries.

### Creating a View

```sql
CREATE VIEW employee_details AS
SELECT e.emp_id, e.first_name, e.last_name, d.dept_name, e.salary
FROM employees e
JOIN departments d ON e.department_id = d.dept_id;
```

### Using a View

```sql
-- Use like a regular table
SELECT * FROM employee_details WHERE salary > 60000;
```

### Updating Data Through Views

```sql
-- Some views allow updates (simple views)
UPDATE employee_details SET salary = 62000 WHERE emp_id = 1;
```

**Explanation:** Updates work if view is based on single table without complex operations.

### Dropping a View

```sql
DROP VIEW employee_details;
```

### OR REPLACE

```sql
CREATE OR REPLACE VIEW employee_details AS
SELECT e.emp_id, e.first_name, e.last_name, d.dept_name, e.salary, e.hire_date
FROM employees e
JOIN departments d ON e.department_id = d.dept_id;
```

**Explanation:** Updates view definition without dropping it first.

### Materialized Views (PostgreSQL)

```sql
-- Creates a physical copy of query results
CREATE MATERIALIZED VIEW dept_summary AS
SELECT department_id, COUNT(*) AS emp_count, AVG(salary) AS avg_salary
FROM employees
GROUP BY department_id;

-- Refresh the data
REFRESH MATERIALIZED VIEW dept_summary;
```

**Explanation:** Unlike regular views, materialized views store actual data and need refreshing.

---

## Indexes

Indexes speed up data retrieval but slow down INSERT/UPDATE/DELETE operations.

### Creating an Index

```sql
CREATE INDEX idx_last_name ON employees(last_name);
```

**Explanation:** Creates an index on last_name column for faster searches.

### Composite Index

```sql
CREATE INDEX idx_name ON employees(last_name, first_name);
```

**Explanation:** Index on multiple columns, useful when frequently searching by both.

### Unique Index

```sql
CREATE UNIQUE INDEX idx_email ON employees(email);
```

**Explanation:** Ensures all values are unique, like UNIQUE constraint but as an index.

### Viewing Indexes

**MySQL:**

```sql
SHOW INDEX FROM employees;
```

**PostgreSQL:**

```sql
\d employees
-- or
SELECT * FROM pg_indexes WHERE tablename = 'employees';
```

**SQLite:**

```sql
.indexes employees
-- or
SELECT * FROM sqlite_master WHERE type = 'index' AND tbl_name = 'employees';
```

### Dropping an Index

**MySQL:**

```sql
DROP INDEX idx_last_name ON employees;
```

**PostgreSQL & SQLite:**

```sql
DROP INDEX idx_last_name;
```

### When to Use Indexes

**Create indexes for:**

- Columns used in WHERE clauses frequently
- Columns used in JOIN conditions
- Columns used in ORDER BY
- Foreign key columns

**Avoid indexes on:**

- Small tables
- Columns with frequent INSERT/UPDATE/DELETE
- Columns with low cardinality (few distinct values)

---

## Transactions

Transactions ensure data integrity by treating multiple operations as a single unit.

### ACID Properties

- **Atomicity**: All or nothing - either all operations succeed or all fail
- **Consistency**: Database remains in valid state
- **Isolation**: Transactions don't interfere with each other
- **Durability**: Committed changes are permanent

### Basic Transaction

```sql
START TRANSACTION;  -- or BEGIN; in PostgreSQL/SQLite

UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;

COMMIT;  -- Save changes
```

**Explanation:** Transfers money from account 1 to account 2. Both operations succeed or both fail.

### Rolling Back

```sql
START TRANSACTION;

UPDATE employees SET salary = salary * 2;

-- Oops, mistake! Undo everything
ROLLBACK;
```

**Explanation:** ROLLBACK undoes all changes since transaction started.

### Savepoints

```sql
START TRANSACTION;

UPDATE employees SET salary = salary + 1000 WHERE department_id = 1;
SAVEPOINT after_dept1;

UPDATE employees SET salary = salary + 1000 WHERE department_id = 2;
SAVEPOINT after_dept2;

-- Undo only department 2 changes
ROLLBACK TO SAVEPOINT after_dept1;

COMMIT;  -- Only department 1 changes are saved
```

**Explanation:** Savepoints allow partial rollback within a transaction.

### Auto-commit

Most databases have auto-commit enabled by default (each statement is automatically committed).

**MySQL - Disable auto-commit:**

```sql
SET autocommit = 0;
-- Now you need explicit COMMIT
```

**PostgreSQL:**

```sql
-- Transactions must be explicitly started with BEGIN
```

### Isolation Levels

```sql
-- MySQL
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;

-- PostgreSQL
BEGIN TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

**Explanation:**

- **READ UNCOMMITTED**: Lowest isolation, can read uncommitted changes (dirty reads)
- **READ COMMITTED**: Can only read committed data
- **REPEATABLE READ**: Same query returns same results within transaction
- **SERIALIZABLE**: Highest isolation, complete isolation from other transactions

---

## Advanced Topics

### CASE Statements

```sql
-- Simple CASE
SELECT first_name,
       last_name,
       CASE department_id
           WHEN 1 THEN 'Engineering'
           WHEN 2 THEN 'Sales'
           WHEN 3 THEN 'Marketing'
           ELSE 'Other'
       END AS department_name
FROM employees;

-- Searched CASE
SELECT first_name,
       last_name,
       salary,
       CASE
           WHEN salary < 40000 THEN 'Low'
           WHEN salary BETWEEN 40000 AND 70000 THEN 'Medium'
           WHEN salary > 70000 THEN 'High'
           ELSE 'Unknown'
       END AS salary_grade
FROM employees;
```

### COALESCE

Returns first non-NULL value.

```sql
SELECT first_name,
       last_name,
       COALESCE(email, phone, 'No contact info') AS contact
FROM employees;
```

**Explanation:** Returns email if not NULL, else phone, else 'No contact info'.

### NULLIF

Returns NULL if two values are equal, otherwise returns first value.

```sql
SELECT product_name,
       price,
       NULLIF(discount_price, price) AS actual_discount
FROM products;
```

**Explanation:** If discount_price equals price (no discount), returns NULL.

### String Functions

```sql
-- Concatenation
SELECT CONCAT(first_name, ' ', last_name) AS full_name FROM employees;
-- PostgreSQL also supports: first_name || ' ' || last_name

-- Uppercase/Lowercase
SELECT UPPER(first_name), LOWER(last_name) FROM employees;

-- Substring
SELECT SUBSTRING(email, 1, 5) FROM employees;  -- First 5 characters

-- Length
SELECT first_name, LENGTH(first_name) AS name_length FROM employees;

-- Trim
SELECT TRIM('  hello  ');  -- 'hello'
SELECT LTRIM('  hello');   -- 'hello  '
SELECT RTRIM('hello  ');   -- '  hello'

-- Replace
SELECT REPLACE(email, '@company.com', '@newdomain.com') FROM employees;

-- Position/Find
SELECT POSITION('@' IN email) FROM employees;  -- PostgreSQL
SELECT LOCATE('@', email) FROM employees;      -- MySQL
SELECT INSTR(email, '@') FROM employees;       -- SQLite
```

### Numeric Functions

```sql
-- Rounding
SELECT ROUND(salary, 0) FROM employees;          -- Round to nearest integer
SELECT ROUND(salary, -3) FROM employees;         -- Round to nearest thousand
SELECT CEILING(salary) FROM employees;           -- Round up
SELECT FLOOR(salary) FROM employees;             -- Round down

-- Absolute value
SELECT ABS(-100);  -- Returns 100

-- Power and Square Root
SELECT POWER(2, 3);  -- 2^3 = 8
SELECT SQRT(16);     -- 4

-- Random
SELECT RANDOM();     -- PostgreSQL/SQLite
SELECT RAND();       -- MySQL

-- Modulo
SELECT MOD(10, 3);   -- Returns 1
SELECT 10 % 3;       -- Alternative syntax
```

### Date Functions

**Current Date/Time:**

```sql
-- MySQL
SELECT NOW(), CURDATE(), CURTIME();

-- PostgreSQL
SELECT NOW(), CURRENT_DATE, CURRENT_TIME;

-- SQLite
SELECT datetime('now'), date('now'), time('now');
```

**Date Arithmetic:**

```sql
-- MySQL
SELECT DATE_ADD(hire_date, INTERVAL 1 YEAR) AS anniversary FROM employees;
SELECT DATE_ADD(hire_date, INTERVAL 6 MONTH) AS six_months FROM employees;
SELECT DATEDIFF(CURDATE(), hire_date) AS days_employed FROM employees;

-- PostgreSQL
SELECT hire_date + INTERVAL '1 year' AS anniversary FROM employees;
SELECT hire_date + INTERVAL '6 months' AS six_months FROM employees;
SELECT CURRENT_DATE - hire_date AS days_employed FROM employees;

-- SQLite
SELECT date(hire_date, '+1 year') AS anniversary FROM employees;
SELECT date(hire_date, '+6 months') AS six_months FROM employees;
SELECT julianday('now') - julianday(hire_date) AS days_employed FROM employees;
```

**Extract Parts:**

```sql
-- MySQL
SELECT YEAR(hire_date), MONTH(hire_date), DAY(hire_date) FROM employees;
SELECT MONTHNAME(hire_date), DAYNAME(hire_date) FROM employees;

-- PostgreSQL
SELECT EXTRACT(YEAR FROM hire_date),
       EXTRACT(MONTH FROM hire_date),
       EXTRACT(DAY FROM hire_date)
FROM employees;
SELECT TO_CHAR(hire_date, 'Month') AS month_name FROM employees;

-- SQLite
SELECT strftime('%Y', hire_date),
       strftime('%m', hire_date),
       strftime('%d', hire_date)
FROM employees;
SELECT strftime('%W', hire_date) AS week_number FROM employees;
```

**Format Dates:**

```sql
-- MySQL
SELECT DATE_FORMAT(hire_date, '%Y-%m-%d') FROM employees;
SELECT DATE_FORMAT(hire_date, '%M %d, %Y') FROM employees;  -- January 15, 2023

-- PostgreSQL
SELECT TO_CHAR(hire_date, 'YYYY-MM-DD') FROM employees;
SELECT TO_CHAR(hire_date, 'Month DD, YYYY') FROM employees;

-- SQLite
SELECT strftime('%Y-%m-%d', hire_date) FROM employees;
SELECT strftime('%m/%d/%Y', hire_date) FROM employees;
```

### Window Functions (Advanced Analytical Queries)

Window functions perform calculations across rows related to current row.

**ROW_NUMBER:**

```sql
SELECT first_name,
       last_name,
       salary,
       ROW_NUMBER() OVER (ORDER BY salary DESC) AS salary_rank
FROM employees;
```

**Explanation:** Assigns unique sequential integers starting from 1.

**RANK and DENSE_RANK:**

```sql
SELECT first_name,
       last_name,
       salary,
       RANK() OVER (ORDER BY salary DESC) AS rank,
       DENSE_RANK() OVER (ORDER BY salary DESC) AS dense_rank
FROM employees;
```

**Explanation:** RANK skips numbers after ties (1,2,2,4), DENSE_RANK doesn't (1,2,2,3).

**PARTITION BY:**

```sql
-- Rank within each department
SELECT first_name,
       last_name,
       department_id,
       salary,
       RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS dept_rank
FROM employees;
```

**Explanation:** Restarts ranking for each department.

**Running Total:**

```sql
SELECT order_date,
       amount,
       SUM(amount) OVER (ORDER BY order_date) AS running_total
FROM orders;
```

**Moving Average:**

```sql
SELECT order_date,
       amount,
       AVG(amount) OVER (
           ORDER BY order_date
           ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
       ) AS moving_avg_3days
FROM orders;
```

**Explanation:** Calculates average of current row and 2 previous rows.

**LAG and LEAD:**

```sql
-- Compare current row with previous/next row
SELECT order_date,
       amount,
       LAG(amount, 1) OVER (ORDER BY order_date) AS previous_amount,
       LEAD(amount, 1) OVER (ORDER BY order_date) AS next_amount,
       amount - LAG(amount, 1) OVER (ORDER BY order_date) AS difference
FROM orders;
```

**NTILE:**

```sql
-- Divide rows into 4 equal groups (quartiles)
SELECT first_name,
       last_name,
       salary,
       NTILE(4) OVER (ORDER BY salary) AS salary_quartile
FROM employees;
```

### Common Table Expressions (CTE)

CTEs create temporary result sets that exist only during query execution.

**Basic CTE:**

```sql
WITH dept_avg AS (
    SELECT department_id, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department_id
)
SELECT e.first_name, e.last_name, e.salary, d.avg_salary
FROM employees e
JOIN dept_avg d ON e.department_id = d.department_id
WHERE e.salary > d.avg_salary;
```

**Explanation:** Finds employees earning more than their department's average.

**Multiple CTEs:**

```sql
WITH
high_earners AS (
    SELECT * FROM employees WHERE salary > 70000
),
dept_stats AS (
    SELECT department_id, COUNT(*) AS emp_count
    FROM high_earners
    GROUP BY department_id
)
SELECT * FROM dept_stats WHERE emp_count > 2;
```

**Recursive CTE:**

```sql
-- Generate numbers from 1 to 10
WITH RECURSIVE numbers AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1 FROM numbers WHERE n < 10
)
SELECT * FROM numbers;

-- Employee hierarchy (managers and subordinates)
WITH RECURSIVE org_chart AS (
    -- Base case: top-level managers
    SELECT employee_id, first_name, manager_id, 1 AS level
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    -- Recursive case: employees with managers
    SELECT e.employee_id, e.first_name, e.manager_id, oc.level + 1
    FROM employees e
    INNER JOIN org_chart oc ON e.manager_id = oc.employee_id
)
SELECT * FROM org_chart ORDER BY level, employee_id;
```

**Explanation:** Recursive CTEs call themselves to traverse hierarchical data.

### UNION, INTERSECT, EXCEPT

**UNION - Combine results (remove duplicates):**

```sql
SELECT first_name FROM employees
UNION
SELECT first_name FROM managers;
```

**UNION ALL - Combine results (keep duplicates):**

```sql
SELECT first_name FROM employees
UNION ALL
SELECT first_name FROM managers;
```

**INTERSECT - Return only common rows:**

```sql
SELECT email FROM employees
INTERSECT
SELECT email FROM newsletter_subscribers;
```

**Explanation:** Finds employees who are also newsletter subscribers.

**EXCEPT (MINUS in Oracle) - Return rows from first query not in second:**

```sql
SELECT email FROM employees
EXCEPT
SELECT email FROM newsletter_subscribers;
```

**Explanation:** Finds employees who are NOT newsletter subscribers.

**Note:** SQLite supports UNION and EXCEPT but INTERSECT support may vary.

### Pivot Tables (MySQL 8.0+, PostgreSQL with CASE)

```sql
-- Transform rows to columns
SELECT
    department_id,
    SUM(CASE WHEN YEAR(hire_date) = 2021 THEN 1 ELSE 0 END) AS hires_2021,
    SUM(CASE WHEN YEAR(hire_date) = 2022 THEN 1 ELSE 0 END) AS hires_2022,
    SUM(CASE WHEN YEAR(hire_date) = 2023 THEN 1 ELSE 0 END) AS hires_2023
FROM employees
GROUP BY department_id;
```

---

## Constraints

Constraints enforce rules on data in tables.

### PRIMARY KEY

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    customer_name VARCHAR(100)
);

-- Composite primary key
CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    quantity INT,
    PRIMARY KEY (order_id, product_id)
);
```

**Explanation:** Composite primary key uses multiple columns to uniquely identify rows.

### FOREIGN KEY

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
```

**With CASCADE options:**

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE
);
```

**Explanation:**

- `ON DELETE CASCADE`: Deleting a customer deletes all their orders
- `ON UPDATE CASCADE`: Updating customer_id updates it in orders too
- Other options: `SET NULL`, `SET DEFAULT`, `RESTRICT`, `NO ACTION`

### UNIQUE

```sql
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    username VARCHAR(50) UNIQUE,
    email VARCHAR(100) UNIQUE
);

-- Add unique constraint to existing table
ALTER TABLE users ADD CONSTRAINT unique_email UNIQUE (email);
```

### NOT NULL

```sql
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    price DECIMAL(10,2) NOT NULL
);
```

### CHECK

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    age INT CHECK (age >= 18),
    salary DECIMAL(10,2) CHECK (salary > 0),
    email VARCHAR(100) CHECK (email LIKE '%@%.%')
);

-- Named constraint
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    price DECIMAL(10,2),
    CONSTRAINT positive_price CHECK (price > 0)
);
```

**Explanation:** CHECK constraints validate data based on conditions.

### DEFAULT

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    order_date DATE DEFAULT CURRENT_DATE,
    status VARCHAR(20) DEFAULT 'pending',
    quantity INT DEFAULT 1
);
```

### Dropping Constraints

```sql
-- Drop named constraint
ALTER TABLE employees DROP CONSTRAINT positive_salary;

-- Drop foreign key (MySQL)
ALTER TABLE orders DROP FOREIGN KEY fk_customer;

-- Drop unique constraint
ALTER TABLE users DROP CONSTRAINT unique_email;
```

---

## Stored Procedures and Functions

### Stored Procedures (MySQL & PostgreSQL)

**MySQL:**

```sql
DELIMITER //

CREATE PROCEDURE GetEmployeesByDept(IN dept_id INT)
BEGIN
    SELECT * FROM employees WHERE department_id = dept_id;
END //

DELIMITER ;

-- Call procedure
CALL GetEmployeesByDept(1);
```

**PostgreSQL:**

```sql
CREATE OR REPLACE PROCEDURE update_salary(
    emp_id INT,
    new_salary DECIMAL
)
LANGUAGE plpgsql
AS $
BEGIN
    UPDATE employees SET salary = new_salary WHERE employee_id = emp_id;
    COMMIT;
END;
$;

-- Call procedure
CALL update_salary(1, 75000);
```

**Procedure with OUT parameters (MySQL):**

```sql
DELIMITER //

CREATE PROCEDURE GetEmployeeCount(
    IN dept_id INT,
    OUT emp_count INT
)
BEGIN
    SELECT COUNT(*) INTO emp_count
    FROM employees
    WHERE department_id = dept_id;
END //

DELIMITER ;

-- Call and get result
CALL GetEmployeeCount(1, @count);
SELECT @count;
```

### User-Defined Functions

**MySQL:**

```sql
DELIMITER //

CREATE FUNCTION calculate_bonus(salary DECIMAL(10,2))
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
    RETURN salary * 0.10;
END //

DELIMITER ;

-- Use function
SELECT first_name, salary, calculate_bonus(salary) AS bonus
FROM employees;
```

**PostgreSQL:**

```sql
CREATE OR REPLACE FUNCTION calculate_bonus(salary NUMERIC)
RETURNS NUMERIC AS $
BEGIN
    RETURN salary * 0.10;
END;
$ LANGUAGE plpgsql;

-- Use function
SELECT first_name, salary, calculate_bonus(salary) AS bonus
FROM employees;
```

**Function with conditional logic (PostgreSQL):**

```sql
CREATE OR REPLACE FUNCTION get_salary_grade(salary NUMERIC)
RETURNS TEXT AS $
BEGIN
    IF salary < 40000 THEN
        RETURN 'Junior';
    ELSIF salary < 70000 THEN
        RETURN 'Mid-Level';
    ELSE
        RETURN 'Senior';
    END IF;
END;
$ LANGUAGE plpgsql;
```

### Dropping Procedures and Functions

```sql
DROP PROCEDURE GetEmployeesByDept;
DROP FUNCTION calculate_bonus;
```

**Note:** SQLite doesn't support stored procedures or user-defined functions in SQL, but you can create them in application code.

---

## Triggers

Triggers automatically execute actions when specific events occur.

### Before INSERT Trigger

**MySQL:**

```sql
DELIMITER //

CREATE TRIGGER before_employee_insert
BEFORE INSERT ON employees
FOR EACH ROW
BEGIN
    SET NEW.created_at = NOW();
    IF NEW.salary < 30000 THEN
        SET NEW.salary = 30000;
    END IF;
END //

DELIMITER ;
```

**PostgreSQL:**

```sql
CREATE OR REPLACE FUNCTION set_created_timestamp()
RETURNS TRIGGER AS $
BEGIN
    NEW.created_at = NOW();
    RETURN NEW;
END;
$ LANGUAGE plpgsql;

CREATE TRIGGER before_employee_insert
BEFORE INSERT ON employees
FOR EACH ROW
EXECUTE FUNCTION set_created_timestamp();
```

### After UPDATE Trigger

**MySQL:**

```sql
DELIMITER //

CREATE TRIGGER after_salary_update
AFTER UPDATE ON employees
FOR EACH ROW
BEGIN
    IF NEW.salary <> OLD.salary THEN
        INSERT INTO salary_history (employee_id, old_salary, new_salary, change_date)
        VALUES (NEW.employee_id, OLD.salary, NEW.salary, NOW());
    END IF;
END //

DELIMITER ;
```

**Explanation:** Logs salary changes to history table whenever an employee's salary is updated.

### After DELETE Trigger

**MySQL:**

```sql
DELIMITER //

CREATE TRIGGER after_employee_delete
AFTER DELETE ON employees
FOR EACH ROW
BEGIN
    INSERT INTO deleted_employees (employee_id, first_name, last_name, deleted_at)
    VALUES (OLD.employee_id, OLD.first_name, OLD.last_name, NOW());
END //

DELIMITER ;
```

### Viewing Triggers

**MySQL:**

```sql
SHOW TRIGGERS;
SHOW TRIGGERS FROM database_name;
```

**PostgreSQL:**

```sql
SELECT * FROM information_schema.triggers;
```

**SQLite:**

```sql
SELECT * FROM sqlite_master WHERE type = 'trigger';
```

### Dropping Triggers

```sql
DROP TRIGGER trigger_name;
```

**Note:** SQLite has limited trigger support compared to MySQL and PostgreSQL but supports basic BEFORE/AFTER INSERT/UPDATE/DELETE triggers.

---

## Performance Optimization

### EXPLAIN - Query Execution Plan

**MySQL:**

```sql
EXPLAIN SELECT * FROM employees WHERE salary > 50000;
EXPLAIN FORMAT=JSON SELECT * FROM employees WHERE department_id = 2;
```

**PostgreSQL:**

```sql
EXPLAIN SELECT * FROM employees WHERE salary > 50000;
EXPLAIN ANALYZE SELECT * FROM employees WHERE department_id = 2;
```

**SQLite:**

```sql
EXPLAIN QUERY PLAN SELECT * FROM employees WHERE salary > 50000;
```

**Explanation:** Shows how database executes the query, helping identify performance bottlenecks.

### Query Optimization Tips

**Use specific columns instead of SELECT \*:**

```sql
-- Slower
SELECT * FROM employees;

-- Faster
SELECT first_name, last_name, salary FROM employees;
```

**Use WHERE instead of HAVING when possible:**

```sql
-- Less efficient
SELECT department_id, AVG(salary)
FROM employees
GROUP BY department_id
HAVING department_id = 2;

-- More efficient
SELECT department_id, AVG(salary)
FROM employees
WHERE department_id = 2
GROUP BY department_id;
```

**Use EXISTS instead of IN for large subqueries:**

```sql
-- Less efficient for large datasets
SELECT * FROM employees
WHERE department_id IN (SELECT dept_id FROM departments WHERE location = 'NYC');

-- More efficient
SELECT * FROM employees e
WHERE EXISTS (
    SELECT 1 FROM departments d
    WHERE d.dept_id = e.department_id AND d.location = 'NYC'
);
```

**Use JOINS instead of subqueries when possible:**

```sql
-- Slower
SELECT e.first_name, (SELECT dept_name FROM departments WHERE dept_id = e.department_id)
FROM employees e;

-- Faster
SELECT e.first_name, d.dept_name
FROM employees e
JOIN departments d ON e.department_id = d.dept_id;
```

**Limit result sets:**

```sql
SELECT * FROM employees LIMIT 100;
```

**Avoid functions on indexed columns in WHERE:**

```sql
-- Slower (index not used)
SELECT * FROM employees WHERE YEAR(hire_date) = 2023;

-- Faster (index can be used)
SELECT * FROM employees
WHERE hire_date >= '2023-01-01' AND hire_date < '2024-01-01';
```

### Index Optimization

```sql
-- Create index for frequently queried columns
CREATE INDEX idx_salary ON employees(salary);

-- Composite index for multiple column searches
CREATE INDEX idx_dept_salary ON employees(department_id, salary);

-- Analyze index usage (MySQL)
SHOW INDEX FROM employees;

-- Remove unused indexes
DROP INDEX idx_unused ON employees;
```

### Query Caching (MySQL)

```sql
-- Check query cache status
SHOW VARIABLES LIKE 'query_cache%';

-- Enable query cache
SET GLOBAL query_cache_size = 1048576;
SET GLOBAL query_cache_type = ON;
```

### Partitioning (MySQL & PostgreSQL)

**MySQL - Range Partitioning:**

```sql
CREATE TABLE sales (
    sale_id INT,
    sale_date DATE,
    amount DECIMAL(10,2)
)
PARTITION BY RANGE (YEAR(sale_date)) (
    PARTITION p2021 VALUES LESS THAN (2022),
    PARTITION p2022 VALUES LESS THAN (2023),
    PARTITION p2023 VALUES LESS THAN (2024),
    PARTITION p_future VALUES LESS THAN MAXVALUE
);
```

**PostgreSQL - Range Partitioning:**

```sql
CREATE TABLE sales (
    sale_id INT,
    sale_date DATE,
    amount DECIMAL(10,2)
) PARTITION BY RANGE (sale_date);

CREATE TABLE sales_2023 PARTITION OF sales
    FOR VALUES FROM ('2023-01-01') TO ('2024-01-01');
```

---

## Security Best Practices

### User Management

**Create User:**

```sql
-- MySQL
CREATE USER 'john'@'localhost' IDENTIFIED BY 'strong_password';

-- PostgreSQL
CREATE USER john WITH PASSWORD 'strong_password';
```

**Grant Privileges:**

```sql
-- MySQL - Grant specific privileges
GRANT SELECT, INSERT, UPDATE ON company.* TO 'john'@'localhost';

-- Grant all privileges
GRANT ALL PRIVILEGES ON company.* TO 'john'@'localhost';

-- PostgreSQL
GRANT SELECT, INSERT, UPDATE ON ALL TABLES IN SCHEMA public TO john;
```

**Revoke Privileges:**

```sql
-- MySQL
REVOKE INSERT, UPDATE ON company.* FROM 'john'@'localhost';

-- PostgreSQL
REVOKE INSERT, UPDATE ON ALL TABLES IN SCHEMA public FROM john;
```

**View Privileges:**

```sql
-- MySQL
SHOW GRANTS FOR 'john'@'localhost';

-- PostgreSQL
\du john
```

**Drop User:**

```sql
-- MySQL
DROP USER 'john'@'localhost';

-- PostgreSQL
DROP USER john;
```

### SQL Injection Prevention

**Never concatenate user input:**

```sql
-- DANGEROUS - Don't do this!
query = "SELECT * FROM users WHERE username = '" + user_input + "'";

-- SAFE - Use parameterized queries
-- Python example with psycopg2
cursor.execute("SELECT * FROM users WHERE username = %s", (user_input,))

-- Python example with sqlite3
cursor.execute("SELECT * FROM users WHERE username = ?", (user_input,))
```

### Password Security

**Hash passwords (never store plain text):**

```sql
-- MySQL - Using SHA2
INSERT INTO users (username, password_hash)
VALUES ('john', SHA2('password123', 256));

-- PostgreSQL - Using pgcrypto extension
CREATE EXTENSION IF NOT EXISTS pgcrypto;

INSERT INTO users (username, password_hash)
VALUES ('john', crypt('password123', gen_salt('bf')));

-- Verify password
SELECT * FROM users
WHERE username = 'john'
AND password_hash = crypt('password123', password_hash);
```

### Backup and Recovery

**MySQL Backup:**

```bash
# Command line backup
mysqldump -u root -p database_name > backup.sql

# Restore
mysql -u root -p database_name < backup.sql
```

**PostgreSQL Backup:**

```bash
# Backup
pg_dump database_name > backup.sql

# Restore
psql database_name < backup.sql
```

**SQLite Backup:**

```sql
-- In SQLite
.backup backup.db

-- Or copy the database file
```

---

## Database-Specific Features

### MySQL Specific

**Storage Engines:**

```sql
-- View available engines
SHOW ENGINES;

-- Create table with specific engine
CREATE TABLE my_table (
    id INT PRIMARY KEY
) ENGINE=InnoDB;

-- MyISAM: Faster reads, no transactions
-- InnoDB: ACID compliant, supports transactions (default)
```

**Full-Text Search:**

```sql
-- Create full-text index
CREATE FULLTEXT INDEX idx_fulltext ON articles(title, content);

-- Search
SELECT * FROM articles
WHERE MATCH(title, content) AGAINST('database optimization');

-- Boolean mode
SELECT * FROM articles
WHERE MATCH(title, content) AGAINST('+mysql -postgresql' IN BOOLEAN MODE);
```

**Auto-Increment Handling:**

```sql
-- Get last inserted ID
SELECT LAST_INSERT_ID();

-- Reset auto-increment
ALTER TABLE employees AUTO_INCREMENT = 1;
```

### PostgreSQL Specific

**Arrays:**

```sql
CREATE TABLE products (
    product_id SERIAL PRIMARY KEY,
    tags TEXT[]
);

INSERT INTO products (tags) VALUES (ARRAY['electronics', 'sale', 'new']);

-- Query array
SELECT * FROM products WHERE 'sale' = ANY(tags);
```

**JSON/JSONB:**

```sql
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY,
    data JSONB
);

INSERT INTO users (data) VALUES
('{"name": "John", "age": 30, "hobbies": ["reading", "gaming"]}');

-- Query JSON
SELECT data->>'name' AS name FROM users;
SELECT * FROM users WHERE data->>'age' = '30';
SELECT * FROM users WHERE data @> '{"hobbies": ["gaming"]}';
```

**Generate Series:**

```sql
-- Generate numbers 1 to 10
SELECT * FROM generate_series(1, 10);

-- Generate dates
SELECT * FROM generate_series(
    '2023-01-01'::date,
    '2023-12-31'::date,
    '1 day'::interval
);
```

**RETURNING Clause:**

```sql
-- Get inserted values
INSERT INTO employees (first_name, last_name, salary)
VALUES ('John', 'Doe', 50000)
RETURNING employee_id, first_name;

-- Get updated values
UPDATE employees SET salary = salary * 1.1
WHERE department_id = 2
RETURNING employee_id, salary;
```

**UPSERT (INSERT ON CONFLICT):**

```sql
INSERT INTO employees (employee_id, first_name, salary)
VALUES (1, 'John', 50000)
ON CONFLICT (employee_id)
DO UPDATE SET salary = EXCLUDED.salary;
```

### SQLite Specific

**Pragmas (Database Configuration):**

```sql
-- View all pragmas
PRAGMA database_list;

-- Foreign key enforcement (off by default)
PRAGMA foreign_keys = ON;

-- Journal mode
PRAGMA journal_mode = WAL;

-- Cache size
PRAGMA cache_size = 10000;
```

**Attach Multiple Databases:**

```sql
-- Attach another database
ATTACH DATABASE 'other.db' AS other_db;

-- Query across databases
SELECT * FROM main.employees
JOIN other_db.departments ON main.employees.dept_id = other_db.departments.dept_id;

-- Detach
DETACH DATABASE other_db;
```

**Date/Time Functions:**

```sql
-- SQLite stores dates as text, numbers, or Julian day
SELECT datetime('now');
SELECT date('now', '+1 month');
SELECT strftime('%Y-%m-%d %H:%M:%S', 'now', 'localtime');
```

---

## Common SQL Patterns and Recipes

### Find Duplicates

```sql
SELECT email, COUNT(*) as count
FROM employees
GROUP BY email
HAVING COUNT(*) > 1;
```

### Remove Duplicates (Keep One)

```sql
-- Using CTE and ROW_NUMBER
WITH cte AS (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY email ORDER BY employee_id) AS rn
    FROM employees
)
DELETE FROM employees
WHERE employee_id IN (SELECT employee_id FROM cte WHERE rn > 1);
```

### Running Total

```sql
SELECT order_date,
       amount,
       SUM(amount) OVER (ORDER BY order_date) AS running_total
FROM orders;
```

### Ranking with Gaps

```sql
SELECT first_name,
       salary,
       RANK() OVER (ORDER BY salary DESC) AS rank
FROM employees;
```

### Find Nth Highest Value

```sql
-- Second highest salary
SELECT MAX(salary) AS second_highest
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);

-- Using LIMIT/OFFSET
SELECT DISTINCT salary
FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;
```

### Swap Values

```sql
-- Swap salaries between two employees
UPDATE employees
SET salary = CASE
    WHEN employee_id = 1 THEN (SELECT salary FROM employees WHERE employee_id = 2)
    WHEN employee_id = 2 THEN (SELECT salary FROM employees WHERE employee_id = 1)
    ELSE salary
END
WHERE employee_id IN (1, 2);
```

### Calculate Age

```sql
-- MySQL
SELECT first_name,
       TIMESTAMPDIFF(YEAR, birth_date, CURDATE()) AS age
FROM employees;

-- PostgreSQL
SELECT first_name,
       DATE_PART('year', AGE(birth_date)) AS age
FROM employees;
```

### Pagination

```sql
-- Page 1 (rows 1-10)
SELECT * FROM products ORDER BY product_id LIMIT 10 OFFSET 0;

-- Page 2 (rows 11-20)
SELECT * FROM products ORDER BY product_id LIMIT 10 OFFSET 10;

-- Page 3 (rows 21-30)
SELECT * FROM products ORDER BY product_id LIMIT 10 OFFSET 20;
```

### Generate Random Sample

```sql
-- MySQL
SELECT * FROM employees ORDER BY RAND() LIMIT 10;

-- PostgreSQL
SELECT * FROM employees ORDER BY RANDOM() LIMIT 10;

-- SQLite
SELECT * FROM employees ORDER BY RANDOM() LIMIT 10;
```

---

## Practice Exercises

### Exercise 1: Basic Queries

```sql
-- Create a database and table
CREATE DATABASE practice;
USE practice;

CREATE TABLE students (
    student_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    major VARCHAR(50),
    gpa DECIMAL(3,2)
);

-- Insert sample data
INSERT INTO students VALUES
(1, 'Alice', 'Johnson', 'Computer Science', 3.8),
(2, 'Bob', 'Smith', 'Mathematics', 3.5),
(3, 'Charlie', 'Brown', 'Computer Science', 3.9),
(4, 'Diana', 'Wilson', 'Physics', 3.7),
(5, 'Eve', 'Davis', 'Mathematics', 3.6);

-- Tasks:
-- 1. Select all students with GPA > 3.7
-- 2. Count students by major
-- 3. Find average GPA by major
-- 4. Find student with highest GPA
```

### Exercise 2: Joins

```sql
-- Create related tables
CREATE TABLE courses (
    course_id INT PRIMARY KEY,
    course_name VARCHAR(100),
    credits INT
);

CREATE TABLE enrollments (
    student_id INT,
    course_id INT,
    grade VARCHAR(2),
    PRIMARY KEY (student_id, course_id)
);

-- Tasks:
-- 1. List all students with their enrolled courses
-- 2. Find students not enrolled in any course
-- 3. Calculate total credits per student
```

### Exercise 3: Advanced Queries

```sql
-- Tasks:
-- 1. Rank students by GPA within each major
-- 2. Find students whose GPA is above their major's average
-- 3. Create a view showing student details with course count
```

---

## Conclusion

This guide covers SQL from basics to advanced topics across MySQL, SQLite, and PostgreSQL. Key takeaways:

1. **Start with fundamentals**: Master CRUD operations, filtering, and sorting
2. **Understand joins**: They're essential for working with relational data
3. **Use indexes wisely**: They speed up reads but slow down writes
4. **Practice transactions**: Ensure data integrity in multi-step operations
5. **Optimize queries**: Use EXPLAIN to understand query execution
6. **Prioritize security**: Use parameterized queries, proper user permissions
7. **Learn database-specific features**: Each system has unique powerful features

**Next Steps:**

- Practice writing queries daily
- Work on real projects with databases
- Study query execution plans
- Learn about database design and normalization
- Explore NoSQL databases for comparison

**Resources:**

- Official documentation for MySQL, PostgreSQL, and SQLite
- Practice platforms: LeetCode, HackerRank, SQLZoo
- Books: "SQL Performance Explained" by Markus Winand

Happy querying! 🚀
