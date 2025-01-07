# Common Snowflake Queries

## Transactions

Transactions allow you to group multiple operations into a single logical unit, ensuring atomicity.

### Key Operations

-   `BEGIN`: Starts a transaction.
-   `COMMIT`: Saves changes made during the transaction.
-   `ROLLBACK`: Reverts changes made during the transaction.

### Examples

```sql
-- Start a transaction
BEGIN;

-- Example: Multiple operations in a transaction
INSERT INTO orders (order_id, customer_id, total_amount)
VALUES (101, 202, 500.00);

UPDATE orders
SET total_amount = 550.00
WHERE order_id = 101;

-- Commit the transaction
COMMIT;

-- Rollback if you want to discard changes
ROLLBACK;

```


## Databases

Databases are containers for schemas, tables, and other objects.

### Common Operations

-   Create, use, show, or drop a database.

### Examples

```sql
-- Create a database
CREATE DATABASE sales_db;

-- Use a specific database
USE DATABASE sales_db;

-- List all databases
SHOW DATABASES;

-- Drop a database
DROP DATABASE sales_db;

```

## Schemas

Schemas help organize database objects like tables, views, and functions.

### Common Operations

-   Create, use, show, or drop schemas.

### Examples

```sql
-- Create a schema
CREATE SCHEMA sales_schema;

-- Use a specific schema
USE SCHEMA sales_schema;

-- Show all schemas in a database
SHOW SCHEMAS;

-- Drop a schema
DROP SCHEMA sales_schema;

```


## Tables

Tables store structured data. Snowflake allows different types of tables, which can be defined based on storage requirements, query performance, and update behavior.

### Types of Tables

1.  **Standard Table**:
    
    -   Regular table without any special storage or performance optimization.
    
    ```sql
    -- Create a standard table
    CREATE TABLE customers (
        customer_id INT,
        customer_name STRING,
        created_at TIMESTAMP
    );
    
    ```
    
2.  **Temporary Table**:
    
    -   A temporary table exists only within the session and is automatically dropped at the end of the session. Useful for intermediate storage.
    
    ```sql
    -- Create a temporary table
    CREATE TEMPORARY TABLE temp_customers (
        customer_id INT,
        customer_name STRING,
        created_at TIMESTAMP
    );
    
    ```
    
3.  **Transient Table**:
    
    -   A transient table is similar to a standard table but does not have a fail-safe feature. It’s useful for short-term storage where recovery is not necessary.
    
    ```sql
    -- Create a transient table
    CREATE TRANSIENT TABLE temp_sales (
        sales_id INT,
        amount DECIMAL,
        sale_date DATE
    );
    
    ```
    
4.  **External Table**:
    
    -   External tables refer to data stored outside of Snowflake, such as in S3 or Azure Blob storage, and allow Snowflake to query external files.
    
    ```sql
    -- Create an external table
    CREATE EXTERNAL TABLE s3_sales_data (
        sales_id INT,
        amount DECIMAL,
        sale_date DATE
    )
    LOCATION = 's3://my-bucket/sales_data/'
    FILE_FORMAT = (TYPE = 'CSV');
    
    ```
    
5.  **Materialized Table**:
    
    -   Snowflake does not natively support materialized tables, but materialized views can be used to store results from complex queries and refresh periodically for better query performance.

#### **Common Table Operations**:

-   Insert, update, query, and drop tables:

```sql
-- Insert data
INSERT INTO customers (customer_id, customer_name, created_at)
VALUES (1, 'Alice', CURRENT_TIMESTAMP);

-- Query data
SELECT * FROM customers;

-- Update data
UPDATE customers
SET customer_name = 'Bob'
WHERE customer_id = 1;

-- Delete data
DELETE FROM customers
WHERE customer_id = 1;

-- Drop a table
DROP TABLE customers;

```


## Clustering with Cluster Keys

Clustering is a method used in Snowflake to organize data in tables to optimize query performance, especially for large tables. You can define clustering using **cluster keys** on a table, which determines how the data is physically stored.

### Cluster Keys

-   Cluster keys are used to control how Snowflake organizes data within tables.
-   They help optimize the performance of queries that filter on clustered columns by reducing the amount of data scanned.
-   Snowflake automatically maintains the clustering of data as new data is inserted, but manual clustering might be needed for performance optimization.

### How to Cluster Using Cluster Keys

```sql
-- Create a table with a cluster key
CREATE TABLE sales_data (
    sale_id INT,
    amount DECIMAL,
    sale_date DATE
)
CLUSTER BY (sale_date);

-- Alter an existing table to add a cluster key
ALTER TABLE sales_data
CLUSTER BY (sale_date);

-- Re-cluster a table manually (useful for large tables that need optimization)
ALTER TABLE sales_data RECLUSTER;

-- Show the clustering key for a table
SHOW TABLES LIKE 'sales_data';

```

### Clustered Tables Example

-   Creating a clustered table can significantly improve the performance of queries that filter or join based on the clustering key (e.g., `sale_date` in the above example).


## Views

Views are virtual tables that contain SQL query logic. They are not physical tables but provide dynamic query results from the underlying tables.

### Types of Views

1.  **Standard View**:
    
    -   A view created from a SQL query, fetching data dynamically each time it’s queried.
    
    ```sql
    CREATE VIEW customer_view AS
    SELECT customer_id, customer_name
    FROM customers
    WHERE created_at > '2024-01-01';
    
    ```
    
2.  **Materialized View**:
    
    -   A precomputed view that stores query results for faster access. It needs to be refreshed periodically to stay up-to-date.
    
    ```sql
    CREATE MATERIALIZED VIEW fast_customer_view AS
    SELECT customer_id, COUNT(order_id) AS order_count
    FROM orders
    GROUP BY customer_id;
    
    -- Refresh the materialized view
    ALTER MATERIALIZED VIEW fast_customer_view REFRESH;
    
    ```
    
3.  **Secure View**:
    
    -   A view that restricts access to sensitive data and ensures only authorized users can see certain columns or rows.
    
    ```sql
    CREATE SECURE VIEW secure_customer_view AS
    SELECT customer_id, customer_name
    FROM customers
    WHERE customer_name LIKE 'A%';
    
    ```
    
4.  **Recursive View**:
    
    -   A view that’s used for hierarchical data structures. Snowflake supports recursive CTEs (common table expressions) instead of traditional recursive views.
    
    ```sql
    WITH RECURSIVE org_chart AS (
        SELECT employee_id, manager_id, 1 AS level
        FROM employees
        WHERE manager_id IS NULL
        UNION ALL
        SELECT e.employee_id, e.manager_id, oc.level + 1
        FROM employees e
        INNER JOIN org_chart oc ON e.manager_id = oc.employee_id
    )
    SELECT * FROM org_chart;
    
    ```
    

### Other View Operations

-   Show all views in a schema:
    
    ```sql
    SHOW VIEWS;
    
    ```
    
-   Drop a view:
    
    ```sql
    DROP VIEW customer_view;
    
    ```
    


## Additional Utility Operations

### Clone Operations

```sql
-- Clone a table (exact copy)
CREATE TABLE cloned_table CLONE customers;

-- Clone a schema
CREATE SCHEMA cloned_schema CLONE sales_schema;

-- Clone a database
CREATE DATABASE cloned_db CLONE sales_db;

```

### Rename Operations

```sql
-- Rename a table
ALTER TABLE customers RENAME TO clients;

-- Rename a view
ALTER VIEW customer_view RENAME TO client_view;

```

### Grant and Revoke Privileges

```sql
-- Grant privileges to a role
GRANT SELECT ON TABLE customers TO ROLE analyst;

-- Revoke privileges
REVOKE SELECT ON TABLE customers FROM ROLE analyst;

```
