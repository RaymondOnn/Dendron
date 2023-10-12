---
id: pp4c1ppmoy3gg3je0elrh3v
title: data_modeling
desc: ''
updated: 1697042260560
created: 1697039451385
---
<https://www.youtube.com/playlist?list=PL9_TNe0XLLUW8yMc7xezr-54JDsNRm081>
<https://towardsdatascience.com/building-better-data-warehouses-with-dimensional-modeling-a-guide-for-data-engineers-422b3cd52df4>

## OLTP vs OLAP

### Online Transaction Processing (OLTP)

- known for its design philosophy of normalization.
- The core idea of normalization is
  - to reduce duplication, saving on storage costs
  - improve write efficiency by  the number of rows to touch if an update/insert/delete operation needs to be performed.

### Online Analytical Processing (OLAP)

- known for its design philosophy of denormalization.
- The core idea of denormalization is to improve query efficiency by reducing complex business logic and big table joins at run time.
- Preparing and writing data often happens beforehand within an ETL process,

## 4 Steps For Successful Dimensional Model Design

### 1. Choose the Business Process

- Stick to the business use case is the most important thing.
- Consulting with the end users is always a wise first move
  - how they parse the data
  - how to interpret the data based on  their domain knowledge,
  - how their current reports are generated
  - understand the pain point in their process.
- Kimball’s Enterprise Data Warehouse Bus Architecture is also an excellent technique to bridge the business use processes and the standard dimensions.
  - The goal is to evaluate what sizes are shared across the fact table.
  - Leveraging the [bus matrix technique](https://www.youtube.com/watch?v=8Ta0iDKIFGI&ab_channel=AI-SURGECLOUD) streamlines the design decision on designing dimensions at a later stage and helps you visualize the overall structure.

### 2. Declare the grain

- The grain refers to the lowest level of information kept in a table.
- The more detailed information is provided, the more flexible it is within the table. Only having aggregated data is impossible to get the detail line level.
- Although, the aggregate view is faster for queries, the lower grain can be aggregated later to provide the same insights, but will take extra time at run time.
- Best to start with the lowest grain

### 3. Identify the Dimensions

- Dimensions are to keep the fact table with more sense for the business and  serve the purpose of the slice & dice.
- Dimensions are often smaller datasets than fact tables. Hence,suitable for broadcast join, since it's small enough to send to every node for joining efficiency.
- The slow change dimension (SCD) is a core dimension designing concept. The most critical ones are SCD Type 2, given its wide adoption in the industry.

4. Identify the Fact
The fact table is the “soul” of dimensional modeling. The fact table keeps the core business in the fact table. Most fact table rows are numeric only, such as total sales quantity, transaction fee, and profit.

There are multiple types of fact tables. You can build a transactional-based fact table or a snapshot fact table like monthly sales.

The core idea of the fact table is to track the data change for a given period. For example, assume Alice has bought the Honeycrisp apple. Then the next day, she returns it because she found a worm inside the apple.

If we take the transaction base view, we will log two rows. The first row is the profit for the $2.99, and the second is -$2.99 (assuming no extra operation cost) since she returns the apple.
If we take a snapshot of the monthly base view, Alice’s purchase won’t impact the business since the net profit from Alice is 0.
The fact table is the tip of the pyramid. It requires all the foundation work to be ready. So having a solid foundation is critical before jumping into the fact table design.

Should I Choose Star Schema and SnowFlake Schema?
The choice of Star schema and Snowflake schema for the data warehouse could be more straightforward for people familiar with the OLTP design and trying to put some level of normalization.

Start schema should be preferable in the majority of cases. There are a few reasons:

We’d want to avoid extra join, which slows down your query performance
The more relationship among dimensions, the more complicated to make manage and maintain data integrity
End users usually don’t have the same familiarity with data schema, and designing the data warehouse should be simple and user-friendly.
The extra cost to store additional data is minimal.
An exception we shall consider Snowflake schema is to save some cost or if the dimensional itself updates frequently, and you need to add normalization to reduce the number of fields to be touched. However, the first goal is to reduce the join to reduce the query time for the end users.

Why Not Keep Everything on a Single Table?
It is also an option that stores all the fields in a single table instead of separating fact and dimension tables.

We could go with a spreadsheet-like single table, and the query could become even faster with columnar storage like Parquet.

The critical criteria to consider is — a dimension table is also shared across multiple fact tables. It would make things easier to manage as the dimension has been referenced in various places.

How about joining? Is dimension to fact table join slow? We have mentioned earlier that “Dimensions often contain much less than the fact tables.”. For distributed computing, the most efficient way is to send the smaller dataset to every single node to avoid a large volume of data getting shuffled. In this case, the join of the fact table is usually minimal, and it’s the engineer to evaluate the trade-off between query performance or data management effort.
