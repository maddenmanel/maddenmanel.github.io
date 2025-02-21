---
title: "Distribute Design Clickhouse"
mathjax: true
toc: true
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - distribute
  - clickhouse
---

## Introduction

ClickHouse, an open-source columnar database management system (DBMS), is designed for online analytical processing (OLAP). It provides real-time data analysis with extremely fast query performance, even on massive datasets. Originally developed by Yandex, ClickHouse has become a popular solution for analytics-heavy use cases in large-scale environments, supporting high throughput and low-latency queries.

ClickHouse's architecture is engineered for distributed processing, offering both horizontal scalability and fault tolerance. This article delves into ClickHouse's architecture and its core features, including data distribution, replication, and fault tolerance mechanisms.

## Key Terminologies

| Term                | Explanation                                                             |
|---------------------|-------------------------------------------------------------------------|
| **Shard**            | A physical subset of data that is stored on a specific set of nodes.    |
| **Replica**          | A copy of a shard's data for fault tolerance and high availability.     |
| **Table**            | A collection of data organized into columns.                            |
| **MergeTree**        | A special type of table that supports fast querying and efficient storage. |
| **Partition**        | A logical division of data within a table, often mapped to a specific time period. |
| **Distributed Table**| A table that abstracts data stored on different nodes in a cluster.     |
| **Replica Synchronization** | The process by which replicas stay in sync with their leader shard.    |
| **Materialized View** | A precomputed view that stores the result of a query to optimize query performance. |

## ClickHouse's Storage Mechanism

### Columnar Storage

ClickHouse's storage engine is optimized for OLAP workloads. It stores data in columnar format, which is ideal for analytical queries that often only access a few columns at a time. This section explores the data distribution, partitioning, and table storage mechanisms in ClickHouse.

What is the the difference between row format and columnar format?

<div class="mermaid">
graph TD
    subgraph Row_Store[Row Storage]
        direction LR
        A1[User ID: 1] --> B1[Name: Zhang San] --> C1[Age: 25] --> D1[Address: Beijing]
        A2[User ID: 2] --> B2[Name: Li Si] --> C2[Age: 30] --> D2[Address: Shanghai]
        A3[User ID: 3] --> B3[Name: Wang Wu] --> C3[Age: 28] --> D3[Address: Guangzhou]
    end

    subgraph Column_Store[Column Storage]
        direction TB
        A4[User ID] --> A5[1] --> A6[2] --> A7[3]
        B4[Name] --> B5[Zhang San] --> B6[Li Si] --> B7[Wang Wu]
        C4[Age] --> C5[25] --> C6[30] --> C7[28]
        D4[Address] --> D5[Beijing] --> D6[Shanghai] --> D7[Guangzhou]
    end

    class Row_Store row_style;
    class Column_Store column_style;

    classDef row_style, stroke-width:2px;
    classDef column_style, stroke-width:2px;
</div>

### Join Operation

```sql
SELECT 
    p.product_name,
    SUM(o.amount) AS total_sales
FROM 
    orders o
JOIN 
    products p
ON 
    o.product_id = p.product_id
GROUP BY 
    p.product_name;
```

This query will be executed in the following steps:

<div class="mermaid">
graph TD
    subgraph Node1[Node 1]
        direction TB
        P1[Read Partition 1 Data] --> A2[Read Data from orders table]
        P2[Read Partition 1 Data] --> A3[Read Data from products table]
        A2 --> B1[Local JOIN]
        A3 --> B1[Local JOIN]
        B1 --> C1[Local Aggregation]
    end

    subgraph Node2[Node 2]
        direction TB
        P3[Read Partition 2 Data] --> A4[Read Data from orders table]
        P4[Read Partition 2 Data] --> A5[Read Data from products table]
        A4 --> B2[Local JOIN]
        A5 --> B2[Local JOIN]
        B2 --> C2[Local Aggregation]
    end

    subgraph NodeN[Node N]
        direction TB
        Pn[Read Partition N Data] --> A6[Read Data from orders table]
        Pn2[Read Partition N Data] --> A7[Read Data from products table]
        A6 --> BN[Local JOIN]
        A7 --> BN[Local JOIN]
        BN --> CN[Local Aggregation]
    end

    C1 --> D1[Global Aggregation]
    C2 --> D1[Global Aggregation]
    CN --> D1[Global Aggregation]

    D1 --> E1[Return Final Result]

    classDef query_step fill:#f9f,stroke:#333,stroke-width:2px;
    class A2,A3,A4,A5,A6,A7,B1,B2,BN,C1,C2,CN,D1,E1 query_step;
</div>

### Global Aggregation

**Global aggregation** is a process that aggregates data from multiple nodes in a distributed system. It involves distributing the data across multiple nodes, each performing local aggregation, and then merging the results to produce a global aggregate.

**(Distribute Data to Compute Nodes)**: This step ensures the data is divided across multiple computing nodes.
**(Hash Grouping at Each Node)**: The hashing and grouping based on keys happen locally at each node.
**(Merge Local Aggregation Results)**: The aggregation results from different nodes are merged to obtain the global sum.

<div class="mermaid">
graph TD
    A[数据输入] --> B[分布式数据分配到各计算节点]
    B --> C[每个节点根据键进行哈希分组]
    C --> D{每个节点的哈希表存储组}
    D --> E[每个节点执行 SUM 聚合]
    E --> F[每个节点保存本地聚合结果]
    F --> G[合并本地聚合结果]
    G --> H[全局聚合结果]
    H --> I[返回最终结果]

    style A fill:#f9f,stroke:#333,stroke-width:4px
    style B fill:#ff9,stroke:#333,stroke-width:4px
    style C fill:#9ff,stroke:#333,stroke-width:4px
    style D fill:#9f9,stroke:#333,stroke-width:4px
    style E fill:#ff9,stroke:#333,stroke-width:4px
    style F fill:#ff9,stroke:#333,stroke-width:4px
    style G fill:#ff9,stroke:#333,stroke-width:4px
    style H fill:#9f9,stroke:#333,stroke-width:4px
</div>

### data structure and advantages or disadvantages

| **Data Type**     | **Description**                                                                         | **Advantages**                                                                                           | **Disadvantages**                                                                                             | **Use Cases**                                                                 | **Query Difficulty**              | **Insert Difficulty**             | **Aggregate Analysis Difficulty**       |
|-------------------|-----------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|----------------------------------|----------------------------------|-----------------------------------------|
| **Array**         | Stores multiple elements of the same data type, such as integers, strings, etc.          | - Flexible, supports various data types. <br> - Efficient for querying and manipulating array elements.    | - Does not support heterogeneous data (only stores elements of the same type). <br> - Performance may degrade with a large number of elements. | - Storing related values like multiple tags, products, etc. <br> - Storing bulk data.                           | Simple, supports direct indexing and element access.           | Easy, insert multiple values directly.       | Aggregation can be resource-intensive with large data.         |
| **Tuple**         | Stores a fixed number of elements with different types.                                 | - Supports heterogeneous data, number of elements is fixed. <br> - Suitable for storing composite data fields. | - Fixed number of elements, not suitable for dynamic changes. <br> - Does not support dynamically altering data structure. | - Storing user information, composite fields, tags, etc. <br> - Suitable for storing structured data.         | Simple, can access elements by position.          | Simple, directly insert a tuple.            | Aggregation can handle individual element aggregation directly.   |
| **LowCardinality** | Used for columns with a low cardinality (few unique values), compressing and mapping values to integers to save memory. | - Significantly reduces memory and storage usage, especially for repeated data. <br> - Increases query speed.  | - Only suitable for low-cardinality data. <br> - May affect query sorting and comparison operations.          | - Suitable for data with a small number of unique values, like cities, gender, categories.   | Good query performance, especially for filtering operations.         | Easy to insert, suitable for bulk insertion of low-cardinality data.    | High efficiency in aggregation.              |
| **UUID**          | Universal Unique Identifier (128-bit identifier), typically used for globally unique identifiers. | - Ensures globally unique identification, avoids ID collisions. <br> - Common in distributed systems.      | - Large storage space, query performance may be slower, especially in sorting and indexing operations. | - ID in distributed systems. <br> - Unique identifier scenarios, such as order IDs, session IDs. | Slower for queries, especially involving sorting.             | Easy to insert, suitable for globally unique identifiers.  | Aggregation is not difficult, but performance may be slower with large data. |
| **DateTime**      | Stores date and time with second precision.                                              | - Suitable for timestamp, time-series data. <br> - Provides rich time handling functions.                | - Relatively larger storage space. <br> - Only precise to the second, no higher precision.                   | - Log analysis, event tracking, time range queries, etc. <br> - Data partitioning by time.    | High performance for range queries based on time.              | Easy to insert, especially with consistent date format.            | High performance for aggregation by time.     |
| **Date**          | Stores only the date (precision to day).                                                 | - Smaller storage space, efficient for date-only data. <br> - Efficient for date queries and aggregation. | - Cannot handle time details (like hours, minutes, seconds).                                               | - Storing date data, such as birth dates, event dates, log dates, etc.          | Extremely efficient for date range queries.              | Simple to insert, just provide date data.              | High performance for day/month aggregation. |
| **String**        | Stores variable-length strings.                                                          | - Highly flexible, supports various text data. <br> - Suitable for textual fields.                        | - Storage and query performance may be slower, especially for large text data. <br> - Not suitable for structured or complex data. | - Storing user names, product descriptions, addresses, etc. <br> - Suitable for unstructured textual data. | Slower for queries involving large datasets.           | Easy to insert, suitable for general text data.                | Performance may be affected with large text data in aggregation.       |
| **Map**           | Stores a collection of key-value pairs, where keys and values can be of different types. | - Extremely flexible, suitable for storing dynamic and changing data structures. <br> - Supports fast lookups by key. | - Higher complexity, querying may be slower, especially when there are many different key-value pairs.         | - Storing dynamic key-value pairs like user attributes, configurations, log information, etc. | Querying is more complex, involves key-value lookups.             | Relatively easy to insert dynamic key-value pairs.               | Aggregation may affect performance when handling a large number of key-value pairs. |
| **Nested**        | Stores hierarchical data, typically used to represent many-to-many or one-to-many relationships, similar to JSON arrays. | - Very suitable for storing complex, hierarchical data. <br> - Can handle complex relationships like users and orders. | - Querying and manipulating nested data may be complex, especially with large datasets; `ARRAY JOIN` may affect performance. | - Storing users with multiple orders, multi-dimensional data, etc. <br> - Suitable for many-to-many and hierarchical data. | Querying requires `ARRAY JOIN` to flatten data, relatively complex.  | Slightly complex, data must match the nested format.  | Aggregation can be done by level but requires `ARRAY JOIN`, which complicates queries. |


### Shard and Partition Distribution in Tables

In ClickHouse, data is distributed across multiple nodes or shards in a cluster. Each shard is a physical unit of storage that may contain multiple partitions, which help organize the data logically.

For example, consider a table `sales_data` that is distributed across three shards. Each shard might contain data for different time periods or geographic regions, allowing queries to target only relevant data for performance optimization.

### MergeTree Tables and Data Storage

The **MergeTree** family of tables is ClickHouse's primary storage engine. Data in MergeTree tables is organized in parts, where each part corresponds to a subset of data stored in files on disk. Each part contains multiple rows of data and is stored in the columnar format.

And this is the data structure of MergeTree tables:

<div class="mermaid">
graph TD
    A[MergeTree Table] --> B[Parts]
    B --> C[Data Stored in Parts Columnar Format]
    C --> D[Sorted by Primary Key]
    D --> E[Primary Key Index]
    B --> F[Background Merging]
    F --> G[Merges Smaller Parts to Larger Parts]
    A --> H[Partitions]
    H --> I[Data Partitioned by Key （e.g., Date,region]
    A --> J[Marking and Removing Old Data]
    J --> K[TTL or Manual Deletion]
    A --> L[Aggregation and Indexing]
    L --> M[Efficient Querying with Aggregated Data]
    classDef mergeTree fill:#ccf,stroke:#333,stroke-width:2px;
    class A,B,C,D,E,F,G,H,I,J,K,L,M mergeTree;
</div>

**MergeTree tables** in ClickHouse are designed to handle large volumes of data while optimizing storage and query performance. Below are key features that contribute to these optimizations:

1. **Primary Key**:  
   The data within each part of a **MergeTree** table is organized and sorted by a **primary key**. The primary key index helps with the efficient retrieval of rows and improves query performance, especially for range queries. It also enables the storage of data in an ordered fashion, which is critical for performing quick searches and aggregations.

2. **Parts Merging**:  
   Over time, as new data is inserted, smaller parts are created. These parts are periodically merged in the background into **larger parts**. This merging process helps to:
   - **Reduce disk fragmentation** by consolidating smaller data parts.
   - **Improve read and write performance** by optimizing how data is accessed.
   - **Maintain optimal storage utilization**, ensuring that large datasets are organized efficiently, leading to faster query execution.

3. **Columnar Storage**:  
   Data is stored in a **columnar format** within each part, making it ideal for **analytical queries**. This format allows ClickHouse to read only the relevant columns for a query, which speeds up the query execution and reduces resource usage.

4. **Partitioning**:  
   The table is divided into **partitions**, which are logical subdivisions of the data, often based on a key (e.g., date or region). Partitioning helps in:
   - Organizing data efficiently for faster querying.
   - Enabling better management and querying of data over time, such as time-series data.
   - Reducing the number of rows to scan, as only the relevant partitions are queried.

5. **TTL (Time-to-Live) and Manual Data Deletion**:  
   **TTL (Time-to-Live)** is a feature that allows data to be automatically deleted after a certain period. This is particularly useful for log or time-series data that becomes irrelevant over time.  
   - **Manual deletion** can also be performed on old data or specific partitions that are no longer needed, freeing up disk space and improving query performance.

6. **Aggregation and Indexing**:  
   **Pre-aggregated data** and **secondary indexes** can be created to speed up queries that involve heavy aggregations or filtering. This helps in:
   - **Efficient querying with aggregated data**, as results can be retrieved directly from pre-aggregated values rather than recalculating them every time a query is run.
   - **Improving query performance** by reducing the need to perform complex calculations during query execution.

### Partitioning Strategy

ClickHouse uses partitioning to divide large datasets into smaller, more manageable chunks. Typically, partitions are created by time, which makes it easier to prune data when querying recent data or performing time-based aggregations.

For example, in a large `clickstream_data` table, partitions could be created by day or week, improving query performance when analyzing recent activity.

## ClickHouse's Internal Architecture

ClickHouse operates on a distributed architecture, where data is spread across multiple nodes in a cluster. This distributed design ensures high availability, fault tolerance, and scalability.

### Key Components

- **Shard**: A unit of storage, representing a physical machine or node in the cluster.
- **Replica**: A copy of a shard's data for redundancy. ClickHouse ensures that replicas are in sync to provide fault tolerance.
- **Distributed Table**: A virtual table that abstracts access to data stored across multiple shards and replicas.
- **ZooKeeper**: Used for managing metadata, leader election, and coordinating replica synchronization.

<div class="mermaid">
graph TD
  A[Producer] --> B[Distributed Table]
  B --> C[Shard]
  C --> D[Replica]
  D --> E[Consumer]
  E --> F[ZooKeeper]
</div>

## Ensuring High Reliability

ClickHouse's high availability and fault tolerance are guaranteed through its replication and synchronization strategies. These mechanisms ensure that even in the case of node failures, data is not lost and the system remains operational.

### Data Replication

Each shard in ClickHouse can have one or more replicas. Replicas store identical data and are synchronized with the leader shard. If a node or replica fails, the system can continue operating by redirecting queries to another replica.

<div class="mermaid">
graph TD
  A[Producer] --> B[Leader Shard]
  B --> C[Replica 1]
  B --> D[Replica 2]
  C --> E[Write Sync]
  D --> E[Write Sync]
</div>

### Replica Synchronization

Replicas synchronize their data using a process where changes made to the leader shard are propagated to the replicas. This ensures that all replicas are consistent and up-to-date with the leader shard.

### Partition Replication and Fault Tolerance

ClickHouse provides fault tolerance through the replication of entire partitions. If a partition becomes unavailable on one replica due to a failure, another replica with the same data can serve the request without any downtime.

### Leader Election and Failover

ZooKeeper is used for leader election in ClickHouse. When a replica fails, ZooKeeper helps determine which replica should take over as the leader. This ensures that the system can continue serving requests without interruption.

<div class="mermaid">
graph TD
  A[ZooKeeper] --> B[Leader Election]
  B --> C[Replica 1]
  B --> D[Replica 2]
  C --> E[Leader Role]
  D --> F[Follower Role]
</div>

## ClickHouse's Distributed Query Execution

ClickHouse can execute queries in parallel across multiple shards and replicas, ensuring that even complex queries over large datasets are processed efficiently.

### Distributed Query Execution Plan

When a query is executed, the **Distributed Table** abstraction allows ClickHouse to create a query plan that targets the relevant shards and replicas. The query is broken down and sent to each shard in the cluster, which processes the query locally. Results from all shards are then aggregated and returned to the user.

### Materialized Views for Optimization

ClickHouse supports **materialized views**, which are precomputed results of a query that are stored in the database. Materialized views are used to speed up query execution, especially for complex aggregations or frequent queries. Once a materialized view is created, ClickHouse updates it automatically when the underlying data changes.

## Conclusion

ClickHouse's distributed architecture and OLAP capabilities make it an ideal choice for real-time data analytics. With features like partitioning, replication, and parallel query execution, it is well-suited for high-performance workloads. ClickHouse ensures high availability and fault tolerance, while also offering powerful mechanisms to scale out horizontally and optimize queries.

The system's robust design ensures that users can efficiently manage and analyze vast amounts of data in real time, making ClickHouse a critical tool for modern data-driven applications.

<script type="module">
  import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs';
  mermaid.initialize({ startOnLoad: true });
</script>
