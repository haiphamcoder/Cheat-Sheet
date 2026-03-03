# 🐬 MySQL Cheat Sheet

A comprehensive guide for Senior DBAs and Developers to master MySQL operations, from basic administration to performance tuning.

---

## 🔐 Access & User Management

| Command | Effect |
| :--- | :--- |
| `mysql -u root -p` | Log in as root user (securely prompts for password) |
| `mysql -h host -u user -p` | Connect to a remote MySQL host |
| `CREATE USER 'user'@'localhost' IDENTIFIED BY 'pass';` | Create a new local user |
| `GRANT ALL PRIVILEGES ON db.* TO 'user'@'host';` | Grant all privileges on a specific database |
| `REVOKE UPDATE ON db.* FROM 'user'@'host';` | Revoke specific permissions from a user |
| `ALTER USER 'user'@'localhost' IDENTIFIED BY 'new_pass';` | Change user password (MySQL 5.7.6+) |
| `DROP USER 'user'@'localhost';` | Delete a user account |
| `SHOW GRANTS FOR 'user'@'localhost';` | View permissions for a specific user |
| `FLUSH PRIVILEGES;` | Reload the privileges from the grant tables |

---

## 📂 Database Operations

| Command | Effect |
| :--- | :--- |
| `SHOW DATABASES;` | List all databases on the server |
| `CREATE DATABASE db_name;` | Create a new database |
| `DROP DATABASE db_name;` | Delete a database (⚠️ Irreversible) |
| `USE db_name;` | Select the database to work with |
| `SELECT DATABASE();` | Show the currently selected database |

---

## 🏗️ Table Management

| Command | Effect |
| :--- | :--- |
| `SHOW TABLES;` | List all tables in the current database |
| `DESCRIBE table_name;` | View table structure (columns, types, keys) |
| `CREATE TABLE table (...) ENGINE=InnoDB;` | Create a new table with specific engine |
| `ALTER TABLE tbl ADD col_name DataType;` | Add a new column to an existing table |
| `ALTER TABLE tbl MODIFY col_name NewDataType;` | Change the data type of a column |
| `ALTER TABLE tbl DROP COLUMN col_name;` | Remove a column from a table |
| `TRUNCATE TABLE table_name;` | Delete all rows and reset auto-increment |
| `DROP TABLE table_name;` | Delete the entire table structure and data |
| `RENAME TABLE old_tbl TO new_tbl;` | Rename an existing table |
| `ALTER TABLE tbl RENAME COLUMN old TO new;` | Rename a column (MySQL 8.0+) |

---

## 💎 Data Types

| Category | Type | Description / Range |
| :--- | :--- | :--- |
| **Numeric** | `TINYINT` | -128 to 127 (Unsigned: 0 to 255) |
| | `INT` / `INTEGER` | -2.1B to 2.1B (Unsigned: 0 to 4.2B) |
| | `BIGINT` | Largest integer (8 bytes) |
| | `DECIMAL(M,D)` | Exact fixed-point (Best for currency) |
| | `FLOAT` / `DOUBLE` | Approximate floating-point values |
| **String** | `CHAR(N)` | Fixed-length string (Right-padded) |
| | `VARCHAR(N)` | Variable-length string (Max 65,535 chars) |
| | `TEXT` | Long string (Up to 64KB) |
| | `LONGTEXT` | Maximum string length (Up to 4GB) |
| **Binary** | `BINARY` / `VARBINARY` | Binary strings (Byte strings) |
| | `BLOB` / `LONGBLOB` | Binary Large Objects (Images/Files) |
| **Date/Time** | `DATE` | 'YYYY-MM-DD' |
| | `DATETIME` / `TIMESTAMP` | 'YYYY-MM-DD HH:MM:SS' |

---

## 🔎 Querying Data (DML)

| Command | Effect |
| :--- | :--- |
| `SELECT * FROM tbl WHERE col = 'val';` | Basic selection with filter |
| `SELECT col1, col2 FROM tbl ORDER BY col1 DESC;` | Select specific columns with sorting |
| `SELECT * FROM tbl LIMIT 10 OFFSET 20;` | Pagination (Fetch 10 rows starting from row 21) |
| `SELECT * FROM tbl WHERE col LIKE 'A%';` | Pattern matching (Starts with 'A') |
| `SELECT category, COUNT(*) FROM tbl GROUP BY category;` | Aggregation by group |
| `SELECT category, AVG(val) FROM tbl GROUP BY category HAVING AVG(val) > 100;` | Group filtering (HAVING works after GROUP BY) |

---

## 🧮 Operators

| Category | Operators |
| :--- | :--- |
| **Logical** | `AND`, `OR`, `NOT`, `XOR`, `&&`, `\|\|`, `!` |
| **Comparison** | `=`, `<>`, `!=`, `<`, `<=`, `>`, `>=` |
| | `IS NULL`, `IS NOT NULL`, `BETWEEN`, `IN`, `LIKE` |
| | `EXISTS`, `IN`, `ALL`, `ANY`, `SOME` |
| **Arithmetic** | `+` (Add), `-` (Sub), `*` (Mult), `/` (Div), `%` / `MOD` (Modulo) |
| **Bitwise** | `&` (AND), `\|` (OR), `^` (XOR), `~` (Invert), `<<` (Left shift) |
| **Compound** | `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `^=`, `\|=` |

---

## 🔗 Joins & Unions

> [!NOTE]
> **INNER JOIN**: Matches in both tables. **LEFT JOIN**: All from left + matches from right. **RIGHT JOIN**: All from right + matches from left.

| Command | Effect |
| :--- | :--- |
| `SELECT * FROM a INNER JOIN b ON a.id = b.a_id;` | Combine rows where keys match in both tables |
| `SELECT * FROM a LEFT JOIN b ON a.id = b.a_id;` | All rows from 'a', and matching rows from 'b' |
| `SELECT col FROM a UNION SELECT col FROM b;` | Combine results and remove duplicates |
| `SELECT col FROM a UNION ALL SELECT col FROM b;` | Combine results including duplicates (Faster) |

---

## ✍️ Modifying Data (DML)

> [!WARNING]
> Always use a **WHERE** clause with `UPDATE` and `DELETE` commands to avoid affecting the entire table.

| Command | Effect |
| :--- | :--- |
| `INSERT INTO tbl (c1, c2) VALUES (v1, v2);` | Insert a single record |
| `INSERT INTO tbl (c1) VALUES (v1), (v2), (v3);` | Bulk insert (Much faster for large datasets) |
| `UPDATE tbl SET c1 = v1 WHERE id = 1;` | Update specific values |
| `DELETE FROM tbl WHERE id = 1;` | Delete specific records |

---

## ⚡ Indexes & Constraints

| Command | Effect |
| :--- | :--- |
| `ALTER TABLE tbl ADD PRIMARY KEY (id);` | Add Primary Key constraint |
| `ALTER TABLE tbl ADD UNIQUE (column);` | Ensure all values in a column are unique |
| `CREATE INDEX idx_name ON tbl (col1, col2);` | Create a composite index for performance |
| `DROP INDEX idx_name ON tbl;` | Remove an index |
| `ALTER TABLE tbl ADD FOREIGN KEY (col) REFERENCES parent(id);` | Define relationship between tables |

---

## ⚙️ Stored Procedures

| Command | Effect |
| :--- | :--- |
| `CREATE PROCEDURE proc_name() BEGIN ... END;` | Define a new stored procedure |
| `CALL proc_name();` | Execute a stored procedure |
| `SHOW PROCEDURE STATUS WHERE Db = 'db_name';` | List all procedures in a database |
| `DROP PROCEDURE IF EXISTS proc_name;` | Delete a stored procedure |

---

## 🛠️ Database Maintenance

| Command | Effect |
| :--- | :--- |
| `mysqldump -u user -p db > backup.sql` | Backup database to a file (Shell command) |
| `mysql -u user -p db < backup.sql` | Restore database from a file (Shell command) |
| `CHECK TABLE table_name;` | Check table for errors |
| `OPTIMIZE TABLE table_name;` | Reorganize physical storage and reclaim space |
| `SELECT table_name, data_length + index_length AS size FROM information_schema.TABLES;` | Check table disk usage |

---

## 🛡️ Transactions

| Command | Effect |
| :--- | :--- |
| `START TRANSACTION;` | Begin a new transaction (Disables auto-commit) |
| `COMMIT;` | Save all changes made during the transaction |
| `ROLLBACK;` | Undo all changes since the transaction started |
| `SET autocommit = 0;` | Disable automatic committing of every statement |

---

## 🎭 Casting & Conversion

| Command | Effect |
| :--- | :--- |
| `CAST(val AS SIGNED);` | Convert value to a signed integer |
| `CAST(val AS DECIMAL(10,2));` | Convert value to decimal with precision |
| `CAST(col AS CHAR);` | Convert column value to string |
| `CONVERT(val, SIGNED);` | Alternative syntax for type conversion |

---

## 🚀 Performance Tuning

| Command | Effect |
| :--- | :--- |
| `EXPLAIN SELECT ...;` | Analyze query execution plan (Index check) |
| `SHOW PROCESSLIST;` | List active threads and running queries |
| `KILL query_id;` | Terminate a long-running or hung query |
| `SHOW VARIABLES LIKE 'max_connections';` | View server configuration variables |
| `SHOW STATUS LIKE 'Threads_connected';` | View real-time server metrics |
| `SET GLOBAL Slow_query_log = 'ON';` | Enable slow query logging for analysis |

---

## 🪄 Common Functions

| Type | Examples |
| :--- | :--- |
| **String** | `CONCAT()`, `SUBSTRING()`, `UPPER()`, `LOWER()`, `TRIM()`, `LENGTH()` |
| **Numeric** | `ROUND()`, `ABS()`, `CEILING()`, `FLOOR()`, `RAND()` |
| **Date/Time** | `NOW()`, `CURDATE()`, `DATEDIFF()`, `DATE_FORMAT()`, `STR_TO_DATE()` |
| **Control** | `IF(expr, v1, v2)`, `COALESCE(v1, v2, ...)`, `CASE WHEN ... THEN ... ELSE ... END` |
