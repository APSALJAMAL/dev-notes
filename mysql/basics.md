# SQL
SQL stands for Structured Query Language

SQL is a standard language for storing, manipulating and retrieving data in databases.

# RDBMS
RDBMS stands for Relational Database Management System.

DDL (Data Definition Language):
Used to define and modify the structure of database objects like tables, schemas, and indexes.
Examples: CREATE, ALTER, DROP, TRUNCATE.

DML (Data Manipulation Language):
Used to manipulate data within tables.
Examples: INSERT, UPDATE, DELETE.

DQL (Data Query Language):
Used to query and retrieve data from the database.
Example: SELECT.

DCL (Data Control Language):
Used to control access to data in the database.
Examples: GRANT, REVOKE.

TCL (Transaction Control Language):
Used to manage transactions in the database.
Examples: COMMIT, ROLLBACK, SAVEPOINT.

# 🧩 SQL Commands Cheat Sheet

## **Database Creation**

### **1. CREATE DATABASE**
```sql
CREATE DATABASE SchoolDB;
```

### **2. USE DATABASE**
```sql
USE SchoolDB;
```

# SQL Data Types & Example Table


## **Numeric Types**
```sql
INT / INTEGER      -- Whole numbers
SMALLINT           -- Small-range integers
BIGINT             -- Large-range integers
DECIMAL(p,s)       -- Fixed-point numbers (precision p, scale s)
NUMERIC(p,s)       -- Same as DECIMAL
FLOAT              -- Approximate floating-point numbers
REAL               -- Single-precision floating-point
DOUBLE PRECISION   -- Double-precision floating-point
```

---

## **Character / String Types**
```sql
CHAR(n)            -- Fixed-length string of length n
VARCHAR(n)         -- Variable-length string up to n characters
TEXT               -- Large variable-length string
```

---

## **Date / Time Types**
```sql
DATE               -- Stores date (YYYY-MM-DD)
TIME               -- Stores time (HH:MM:SS)
DATETIME           -- Stores date and time
TIMESTAMP          -- Stores date and time with automatic update on change
YEAR               -- Stores year only
```

---

## **Boolean / Logical Types**
```sql
BOOLEAN            -- Stores TRUE or FALSE
```

---

## **Binary / Large Objects (LOBs)**
```sql
BINARY(n)          -- Fixed-length binary data
VARBINARY(n)       -- Variable-length binary data
BLOB               -- Binary Large Object
CLOB               -- Character Large Object
```

---

## **Other / Special Types**
```sql
ENUM('val1','val2',...) -- Stores one value from a set
SET('val1','val2',...)  -- Stores multiple values from a set
JSON                     -- Stores JSON formatted data
UUID                     -- Universally unique identifier
```

---

# SQL Data Types Example Table

```sql
CREATE TABLE EMPLOYEE_EXAMPLE (
  empId INT PRIMARY KEY,               -- 1001
  empName VARCHAR(50) NOT NULL,        -- 'Alice'
  dept CHAR(10),                        -- 'Sales'
  salary DECIMAL(10,2),                 -- 55000.75
  bonus FLOAT,                           -- 1500.50
  isPermanent BOOLEAN,                   -- TRUE
  joinDate DATE,                         -- '2025-01-15'
  joinTime TIME,                         -- '09:30:00'
  lastLogin DATETIME,                    -- '2025-10-07 10:30:00'
  updatedAt TIMESTAMP,                   -- '2025-10-07 10:35:12'
  profilePic BLOB,                       -- binary data
  resume CLOB,                           -- 'Employee resume text...'
  empType ENUM('FullTime','PartTime','Contract'),  -- 'FullTime'
  skills SET('Java','Python','SQL'),                 -- 'Java,SQL'
  extraData JSON,                         -- '{"hobby":"reading","city":"NY"}'
  uuidColumn UUID                         -- '550e8400-e29b-41d4-a716-446655440000'
);
```

---

### **Explanation with Examples**

| Column       | Data Type                        | Example Value                         |
|-------------|---------------------------------|--------------------------------------|
| empId       | INT                              | 1001                                  |
| empName     | VARCHAR(50)                      | 'Alice'                               |
| dept        | CHAR(10)                         | 'Sales'                               |
| salary      | DECIMAL(10,2)                    | 55000.75                              |
| bonus       | FLOAT                            | 1500.50                               |
| isPermanent | BOOLEAN                          | TRUE                                   |
| joinDate    | DATE                             | '2025-01-15'                          |
| joinTime    | TIME                             | '09:30:00'                             |
| lastLogin   | DATETIME                         | '2025-10-07 10:30:00'                 |
| updatedAt   | TIMESTAMP                        | '2025-10-07 10:35:12'                 |
| profilePic  | BLOB                             | binary data                            |
| resume      | CLOB                             | 'Employee resume text...'              |
| empType     | ENUM('FullTime','PartTime','Contract') | 'FullTime'                      |
| skills      | SET('Java','Python','SQL')       | 'Java,SQL'                             |
| extraData   | JSON                             | '{"hobby":"reading","city":"NY"}'     |
| uuidColumn  | UUID                             | '550e8400-e29b-41d4-a716-446655440000' |


## **DDL Commands**

### **1. CREATE**
```sql
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    ....
);
```

**Example**
```sql
CREATE TABLE EMPLOYEE (
  empId INTEGER PRIMARY KEY,
  name TEXT NOT NULL,
  dept TEXT NOT NULL
);
```

---

### **2. ALTER**
```sql
ALTER TABLE Table_name ADD column_name datatype;
```

**Example**
```sql
INSERT INTO EMPLOYEE VALUES (0001, 'Dave', 'Sales');
```

---

### **3. TRUNCATE** 

Removes all rows from a table, but keeps the table structure.
```sql
TRUNCATE TABLE table_name;
```

---

### **4. DROP**
Deletes the entire table
```sql
DROP TABLE table_name;
```

---

### **5. RENAME**
```sql
RENAME TABLE table_name1 TO new_table_name1;
```

---

### **6. COMMENT**

**Single-Line Comment**
```sql
-- Line1;
```

**Multi-Line Comment**
```sql
/* Line1,
   Line2 */
```

---

## **DML Commands**

### **1. INSERT**
```sql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```

**Note:** Column names are optional.

**Example**
```sql
INSERT INTO EMPLOYEE VALUES (0001, 'Ava', 'Sales');
```

---

### **2. SELECT**
```sql
SELECT column1, column2, ...
FROM table_name
[WHERE condition];
```

**Example**
```sql
SELECT * FROM EMPLOYEE WHERE dept = 'Sales';
```

---

### **3. UPDATE**
```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

**Example**
```sql
UPDATE EMPLOYEE SET dept = 'Sales' WHERE empId = '0001';
```

---

### **4. DELETE**
```sql
DELETE FROM table_name WHERE condition;
```

**Example**
```sql
DELETE FROM EMPLOYEE WHERE empId = '0001';
```

---

## **Indexes**

### **1. CREATE INDEX**
```sql
CREATE INDEX index_name ON table_name(column_name);
```

**To Create Unique Index**
```sql
CREATE UNIQUE INDEX index_name ON table_name(column_name);
```

---

### **2. DROP INDEX**
```sql
DROP INDEX index_name ON table_name;
```

---

## **Views**

### **1. Create a View**
```sql
CREATE VIEW View_name AS
Query;
```

---

### **2. How to Call a View**
```sql
SELECT * FROM View_name;
```

---

### **3. Altering a View**
```sql
ALTER VIEW View_name AS
Query;
```

---

### **4. Deleting a View**
```sql
DROP VIEW View_name;
```

---

## **Triggers**

### **1. Create a Trigger**
```sql
CREATE TRIGGER trigger_name trigger_time trigger_event
ON tbl_name FOR EACH ROW [trigger_order] trigger_body;
```

**Where**
```sql
trigger_time: { BEFORE | AFTER }
trigger_event: { INSERT | UPDATE | DELETE }
trigger_order: { FOLLOWS | PRECEDES }
```

---

### **2. Drop a Trigger**
```sql
DROP TRIGGER [IF EXISTS] trigger_name;
```

---

## **Stored Procedures**

### **1. Create a Stored Procedure**
```sql
CREATE PROCEDURE sp_name(p1 datatype)
BEGIN
  /*Stored procedure code*/
END;
```

---

### **2. How to Call a Stored Procedure**
```sql
CALL sp_name;
```

---

### **3. How to Delete a Stored Procedure**
```sql
DROP PROCEDURE sp_name;
```

---

## **Joins**

### **1. INNER JOIN**
```sql
SELECT * FROM TABLE1 INNER JOIN TABLE2 WHERE condition;
```

---

### **2. LEFT JOIN**
```sql
SELECT * FROM TABLE1 LEFT JOIN TABLE2 ON condition;
```

---

### **3. RIGHT JOIN**
```sql
SELECT * FROM TABLE1 RIGHT JOIN TABLE2 ON condition;
```

---

### **4. CROSS JOIN**
```sql
SELECT select_list FROM TABLE1 CROSS JOIN TABLE2;
```
