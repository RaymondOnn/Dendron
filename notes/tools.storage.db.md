---
id: ghu3atq2rmq6jc8uupd1z29
title: databases
desc: ''
updated: 1708931284043
created: 1698230588088
---

### How Databases Read Data From Disk

-   Lowest data level of a disk is organized into blocks.
-   Blocks are the smallest size a computer is capable of reading from a disk at one time.
-   A database loads all necessary data after it searches the blocks that store the data it’s looking for.
-   Afterward, it will read these data blocks from the disk, which are now available to work with.
-   The fewer the blocks to read, the faster the operation since less data to read and therefore fewer blocks or because the necessary data is stored in fewer blocks.

If the data your query is requesting is stored in the same blocks, the database will return it much faster than if it had to search through many different blocks. That’s the core concept you need to keep in mind as we discuss whether it’s best to store information on disk by rows or by columns.

### Row vs Column database

| Row-oriented                                 | Column-oriented                                         |
| -------------------------------------------- | --------------------------------------------------------| 
| Inserting and deleting data is fast and easy | Inserting and deleting data can be slow, especially if the table has many columns |
| Best for OLTP applications                   | Best for OLAP applications                              |
| Aggregating data is slow and inefficient     | The best solution for aggregating data                  |
| Insufficient compression                     | High compression because of data similarity             |
| Requires more space to store data            | Requires less space to store data                       |

#### Row-Oriented Databases

-   In row-oriented databases, often called traditional databases, rows are stored in sequence. This means that rows immediately follow each another.
-   **All columns in a single row are stored together** on the same page as long as the row size is smaller than the page size.
-   This provides excellent performance when querying multiple columns of a single row, as is typical in online transactional processing (OLTP) applications. PostgreSQL and MySQL are some of the most common row-oriented databases.
-   To make it easier to find the single row you’re looking for, indexes are typically created in a row-oriented database in columns with unique keys or uncommon values like email addresses or names.
-   However, indexes are usually not useful in analytical queries that span many rows. For example, if you have a complex query, it might lead to a sequential scan, which can negatively impact the performance.

#### How a Row-Oriented Database Works

-   In a row-oriented database, data is written and stored on disk one row at a time.
-   In our example, we have John’s name first, then his gender, his country, and finally his age. Then the next row will follow, and so on.

    | Name  | Gender | Country   | Age |
    | ----- | ------ | --------- | --- |
    | John  | Male   | USA       | 63  |
    | Mary  | Female | Canada    | 29  |
    | James | Male   | Australia | 48  |

-   The data is stored in this order in different blocks, and each person’s information is likely to be in the same block.

    -   So all of John’s information is grouped, as is Mary’s, etc.
    -   Hence, when running a query requesting John’s data, the database won’t have to load much information into memory.
        ```raw
        John_Male_USA_63	Mary_Female_Canada_29	James_Male_Australia_48
        ```

-   Writing new data into row-oriented databases is easy and fast. We simply append the person’s name at the very end of the block.
    ```raw
    John_Male_USA_63	Mary_Female_Canada_29	James_Male_Australia_48	Kate_Female_USA_52
    ```

### Column-Oriented Database

-   Imagine you have a table with hundreds of rows and columns.
-   If you make a complex query, the information you need will be in several blocks, so you’ll have to go through the whole database to get a result.
-   Creating a composite index won’t work because it’s impossible to cover all possible combinations in an analytic environment.

#### How a Column-Oriented Database Works

-   This is where column-oriented databases like Amazon Redshift and BigQuery come in handy.
-   Columnar databases store data from one column together on disk.

    -   This means that all names form one group, genders form another, etc.
    -   If, for example, you need to access all names, you can do so quickly and efficiently.

        | Name  | Gender | Country   | Age |
        | ----- | ------ | --------- | --- |
        | John  | Male   | USA       | 63  |
        | Mary  | Female | Canada    | 29  |
        | James | Male   | Australia | 48  |

-   Because each column is stored together, you only have to read the blocks you need to get your response without having to read through unnecessary data.

#### Why Columnar Storage

-   **Compression Efficiency**: Columnar storage allows for better compression techniques. Since column values are often of the same type, compression algorithms can be tailored to that type, resulting in higher compression ratios. This reduces storage requirements and speeds up data transfers.
-   **Column Pruning**: When executing queries, columnar databases can skip irrelevant columns, reducing I/O and improving query performance. In row-based storage, entire rows must be read, even if only a few columns are needed.
-   **Aggregation Performance**: Columnar storage is highly efficient for aggregate queries, as aggregations involve operations on single columns. This leads to faster query performance for analytics tasks.
-   **Predicate Pushdown**: Columnar databases can apply filters early in the query execution process by analyzing metadata, minimizing the amount of data read from storage. This feature significantly speeds up query processing.
-   **Analytics and Data Warehousing**: Columnar storage is well-suited for analytical workloads, reporting, and data warehousing. It allows for rapid analysis and reporting on large datasets.
-   

-   **Schema Evolution**: Columnar storage formats like Parquet support schema evolution, enabling the addition of new columns or changes to existing columns without disrupting existing data.

#### Bad for writes

-   If you need to insert a single row of data into a column-oriented database, each column of the new row has to be appended to its corresponding block. Potentially, this could lead to performance issues, especially if there are many columns in the table.
-   Furthermore, the same difficulty occurs when you need to delete a row from your database. To successfully delete a row, you have to delete the records from all columns, which might take time if you have a large file.
