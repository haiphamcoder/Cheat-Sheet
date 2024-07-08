# HBase Cheat Sheet

## 1. HBase Basics

- HBase is a distributed, scalable, and consistent NoSQL database built on top of Hadoop.
- It is designed to handle large amounts of structured and semi-structured data.
- HBase provides random, real-time read/write access to your Big Data.

## 2.HBase Data Model

- HBase organizes data into tables, which consist of rows and columns.
- Each row is identified by a unique row key.
- Columns are grouped into column families, which are defined when creating a table.
- HBase supports dynamic column addition, so you can add columns on the fly.

## 3. HBase Shell Commands

- Start HBase shell: `$ hbase shell`
- List all tables: `list`
- Create a table: `create 'table_name', 'column_family'`
- Disable a table: `disable 'table_name'`
- Enable a table: `enable 'table_name'`
- Describe a table: `describe 'table_name'`
- Put data into a table: `put 'table_name', 'row_key', 'column_family:column', 'value'`
- Get data from a table: `get 'table_name', 'row_key'`
- Scan a table: `scan 'table_name'`
- Delete a row from a table: `delete 'table_name', 'row_key'`

## 4. HBase Java API

- HBase provides a Java API for programmatic access to HBase.
- The API includes classes like HBaseConfiguration, HTable, Put, Get, Scan, etc.
- Use HBaseConfiguration to set up the HBase configuration.
- Use HTable to interact with tables (create, put, get, scan, etc.).
- Use Put to insert data into a table.
- Use Get to retrieve data from a table.
- Use Scan to perform a range scan on a table.

## 5. HBase Performance Tuning

- Use appropriate hardware and network configurations for optimal performance.
- Tune HBase configuration parameters like block size, memstore size, etc.
- Monitor HBase using tools like HBase shell, HBase web UI, and Hadoop metrics.
- Enable compression to reduce disk space usage and improve read/write performance.
- Use appropriate data modeling techniques like column families, row key design, etc.

## 6. HBase Integration with Hadoop Ecosystem

- HBase integrates well with other components of the Hadoop ecosystem.
- Use HBase as a data source/sink for MapReduce jobs.
- Use Apache Hive or Apache Phoenix to query HBase data using SQL-like syntax.
- Use Apache Spark for real-time analytics on HBase data.
- Use Apache Kafka for streaming data ingestion into HBase.

Remember, this cheat sheet provides a brief overview of HBase. For more detailed information, refer to the official HBase documentation.