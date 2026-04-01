# SQL Fundamentals

## 🗄️ Database Types

| Type | Language | Structure | Best Used When |
|------|----------|-----------|----------------|
| **Relational** | SQL | Tabular — rows and columns | Data arrives in a consistent, structured format |
| **Non-Relational** | NoSQL | Non-tabular (documents, key-value, etc.) | Data format varies or is unstructured |

---

## 🔗 Relational Database Concepts

### Keys

| Key Type | Description | Example |
|----------|-------------|---------|
| **Primary Key** | Unique identifier for each row | `id = 1` → Rome / Italy |
| **Foreign Key** | Column that references the primary key of another table — links two tables together | `city_id` in `alumni` table references `id` in `cities` table |

**Example — two linked tables:**

```
Table: cities                       Table: alumni
┌────┬────────┬─────────┐           ┌────┬────────┬──────────┬─────────┐
│ id │ name   │ country │           │ id │ name   │ surname  │ city_id │
├────┼────────┼─────────┤           ├────┼────────┼──────────┼─────────┤
│  1 │ Rome   │ Italy   │  ←──────  │  1 │ Mario  │ Rossi    │    1    │
│  2 │ Paris  │ France  │  ←──────  │  2 │ Lily   │ Vou      │    2    │
└────┴────────┴─────────┘           └────┴────────┴──────────┴─────────┘
```

---

## ⚙️ DBMS & SQL

| Term | Description | Examples |
|------|-------------|---------|
| **DBMS** | Database Management System — interface between user and database. Used to retrieve, update and manage data | MySQL, MongoDB, Oracle DB, MariaDB |
| **SQL** | Structured Query Language — used to query, define and manipulate data | — |

---

## 💻 MySQL CLI Commands

```bash
mysql -u root -p        # Connect to MySQL as root (prompts for password)
```

```sql
CREATE DATABASE bookshop;           -- Create a database
USE bookshop;                       -- Switch to a database
DROP DATABASE bookshop;             -- Delete a database
```

---

## 📐 Table Management

```sql
-- Create a table
CREATE TABLE book_inventory (
    book_id        INT AUTO_INCREMENT PRIMARY KEY,  -- auto-incrementing unique ID
    book_name      VARCHAR(255) NOT NULL,            -- text field, max 255 chars, required
    publication_date DATE                            -- date field
);

-- View table structure
DESCRIBE book_inventory;

-- Add a new column
ALTER TABLE book_inventory ADD author VARCHAR(100);
```

---

## 📝 CRUD Operations

### Create — INSERT

```sql
INSERT INTO book_inventory (book_name, publication_date, author)
VALUES ('The Pragmatic Programmer', '1999-10-20', 'Andrew Hunt');
```

### Read — SELECT

```sql
SELECT * FROM book_inventory;                           -- All columns, all rows
SELECT book_name, author FROM book_inventory;           -- Specific columns only
SELECT * FROM book_inventory WHERE author = 'Andy Weir'; -- Filter by condition
```

### Update — UPDATE

```sql
UPDATE book_inventory
SET author = 'Andrew Hunt'
WHERE book_id = 1;
```

### Delete — DELETE

```sql
DELETE FROM book_inventory WHERE book_id = 3;
```

---

## 🔧 Clauses

```sql
-- DISTINCT — remove duplicate results
SELECT DISTINCT author FROM book_inventory;

-- ORDER BY — sort results
SELECT * FROM book_inventory ORDER BY publication_date ASC;   -- ascending
SELECT * FROM book_inventory ORDER BY publication_date DESC;  -- descending

-- GROUP BY — group rows with the same value and aggregate them
SELECT author, COUNT(*) AS total_books
FROM book_inventory
GROUP BY author;

-- HAVING — filter after GROUP BY (WHERE filters before grouping, HAVING filters after)
SELECT author, COUNT(*) AS total_books
FROM book_inventory
GROUP BY author
HAVING total_books > 2;
```

---

## 🔍 Operators

### Logical Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `LIKE` | Filter by pattern — `%` is wildcard | `WHERE book_name LIKE '%Python%'` |
| `AND` | All conditions must be true | `WHERE author = 'Andy' AND year = 2021` |
| `OR` | At least one condition must be true | `WHERE author = 'Andy' OR author = 'Hunt'` |
| `NOT` | Returns true if condition is false | `WHERE NOT author = 'Andy'` |
| `BETWEEN` | Value within a range (inclusive) | `WHERE book_id BETWEEN 2 AND 5` |

### Comparison Operators

| Operator | Meaning |
|----------|---------|
| `=` | Equal to |
| `!=` | Not equal to |
| `<` | Less than |
| `<=` | Less than or equal to |
| `>` | Greater than |
| `>=` | Greater than or equal to |

---

## 🛠️ Functions

### String Functions

```sql
-- CONCAT() — combine text from multiple columns
SELECT CONCAT(book_name, ' by ', author) AS full_title
FROM book_inventory;
-- Output: "The Martian by Andy Weir"

-- GROUP_CONCAT() — concatenate values from multiple rows into one field
SELECT author, GROUP_CONCAT(book_name SEPARATOR ', ') AS books
FROM book_inventory
GROUP BY author;
-- Output: Andy Weir | The Martian, Project Hail Mary

-- SUBSTRING() — extract part of a string from a position (with optional length)
SELECT SUBSTRING(publication_date, 1, 4) AS pub_year
FROM book_inventory;
-- Extracts the year from a date: "1999-10-20" → "1999"

-- LENGTH() — count characters in a string (including spaces and punctuation)
SELECT book_name, LENGTH(book_name) AS name_length
FROM book_inventory;
```

### Aggregate Functions

```sql
-- COUNT() — number of records
SELECT COUNT(*) AS total_books FROM book_inventory;

-- SUM() — sum of a numeric column (ignores NULL)
SELECT SUM(price) AS total_value FROM book_inventory;

-- MAX() / MIN() — largest or smallest value in a column
SELECT MAX(publication_date) AS newest_book FROM book_inventory;
SELECT MIN(publication_date) AS oldest_book FROM book_inventory;
```

---

## 📋 Quick Reference — Full Query Structure

```sql
SELECT DISTINCT col1, COUNT(*) AS total    -- what to return
FROM tableName                             -- from which table
WHERE col1 LIKE '%value%'                  -- filter rows (before grouping)
GROUP BY col1                              -- group rows
HAVING total > 5                           -- filter groups (after grouping)
ORDER BY total DESC                        -- sort results
LIMIT 10;                                  -- limit number of results
```

> **WHERE vs HAVING:** `WHERE` filters individual rows before grouping. `HAVING` filters groups after `GROUP BY` has been applied.
