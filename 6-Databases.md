# Databases

In **Snowflake**, databases are stored in a highly scalable and flexible way, leveraging a **multi-cloud architecture** built on top of cloud providers like **AWS**, **Azure**, and **Google Cloud**. Snowflake separates its storage layer from its compute layer, providing significant advantages in terms of performance, scalability, and cost efficiency.


## Storage Layer in Snowflake

-   The **storage layer** is responsible for persisting data in Snowflake. This layer uses the cloud provider’s storage infrastructure (e.g., **Amazon S3**, **Azure Blob Storage**, or **Google Cloud Storage**) to store data in **distributed object storage**.
-   The storage layer is **fully managed** by Snowflake, meaning that users do not need to worry about infrastructure management, capacity planning, or scaling.
-   **Data is stored in a columnar format (PAX)** to optimize for query performance, especially for analytical workloads. This allows for compression and efficient querying of large datasets.
-   Snowflake employs **automatic data partitioning** and **compression** to optimize the storage cost and performance. It organizes data into **micro-partitions**, which are small, immutable units of storage (typically around 16 MB per micro-partition).

## Databases and Schemas in Snowflake

-   **Databases** in Snowflake are logical containers that hold schemas, which in turn contain tables, views, and other objects.
    
    -   **Schema**: A schema is a structure within a database that contains database objects like tables, views, and other related objects. It is similar to how schemas are used in traditional relational databases.
    -   **Tables**: Tables hold the actual data. Each table is made up of columns and rows, with the columnar storage format being used for efficiency.
    
    In Snowflake:
    
    -   A **database** is a logical construct for organizing and separating data.
    -   A **schema** within the database helps in logically grouping related tables.


## How Data is Stored in Snowflake

### Micro-Partitions
   -   Snowflake automatically divides large tables into **micro-partitions**—small, immutable chunks of data (typically around 16 MB in size). This segmentation allows for efficient data retrieval, compression, and parallel query processing.
    -   Micro-partitions are stored in a columnar format and are internally managed by Snowflake, so users do not need to manually manage partitioning or indexing.
    
### Data Compression
   -   Data in Snowflake is highly compressed, which significantly reduces storage costs.
   -   Since Snowflake uses **columnar storage**, it can apply compression algorithms that are tailored for each column’s data type (e.g., numeric, text, or date).
   -   Snowflake’s compression is automatic and transparent to the user, and it minimizes the need for additional configuration.
   
### Versioning (Time Travel)
   -   Snowflake stores **historical versions of data** by retaining older versions of micro-partitions.
   -   With **Time Travel**, users can access and query historical data up to 90 days in the past (depending on the account settings). This is useful for recovering lost data or auditing data changes.
   -   Snowflake uses **metadata storage** to track changes to data over time.
### Zero-Copy Cloning
   -   Snowflake allows users to **clone databases, schemas, or tables** without physically copying the data. This means that clones are created with no extra storage cost initially.
   -   The cloned objects reference the original data, but any modifications made to the clone do not affect the original data, and vice versa. Over time, as data is modified in either the clone or the original, new micro-partitions are created, and storage cost grows.

## How Snowflake Manages Storage

### Separation of Compute and Storage
    
   -   Snowflake's architecture separates storage from compute, meaning that users can scale storage independently of compute power.
   -   The storage layer handles all the data, while the **compute layer** (virtual warehouses) handles query processing. This separation allows for more efficient scaling and cost optimization.
### Automatic Scaling
    
   -   Snowflake automatically scales storage without requiring manual intervention. As your data grows, Snowflake will increase the storage capacity to accommodate the new data.
   -   Since storage is decoupled from compute, scaling storage does not impact ongoing queries or data processing.
### Data Retention and Garbage Collection
   -   When data is updated or deleted, Snowflake maintains the older versions in the storage layer as part of its **Time Travel** and **Fail-safe** features.
   -   After the Time Travel retention period expires, Snowflake performs **garbage collection** to remove outdated or obsolete data. This helps free up storage and ensures only the necessary data is retained.


## High Availability and Redundancy
-   **Snowflake** ensures **high availability** and **data redundancy** by storing data in multiple **availability zones** within the cloud provider’s infrastructure.
-   If one availability zone goes down, Snowflake can still access the data from another zone, ensuring that your data is always available and protected against failure.


## Security and Encryption in Storage

-   **Encryption**: Snowflake ensures that all data is encrypted both in **transit** and **at rest**.
    -   Data stored in the storage layer is encrypted using **AES-256 encryption**.
    -   Snowflake handles key management, but users can also use their own keys (using **customer-managed keys**) if desired.
-   **Data Access Control**: Snowflake uses **role-based access control (RBAC)** to manage access to databases, schemas, and tables. This ensures that only authorized users can access specific data.


## Summary of How Snowflake Stores Data

-   **Storage Layer**: Snowflake stores data in **cloud storage** (e.g., AWS S3, Azure Blob Storage, or Google Cloud Storage) using a **columnar format** and **micro-partitions** to optimize query performance and storage efficiency.
-   **Data Compression**: Data is highly compressed, reducing the cost of storage.
-   **Separation of Compute and Storage**: Snowflake’s architecture separates the storage layer from the compute layer, allowing for independent scaling of both.
-   **Versioning**: Snowflake stores historical data for **Time Travel**, enabling access to previous versions of data for up to 90 days.
-   **Data Cloning**: Users can create **zero-copy clones** of data without physical duplication, which helps optimize storage costs.
-   **Redundancy and Availability**: Snowflake stores data in multiple availability zones to ensure high availability and durability.
-   **Security**: Snowflake encrypts all data and provides robust access control through **role-based access**.
