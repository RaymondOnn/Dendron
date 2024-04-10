---
id: 9cbva3aptjgcvocn92adfb6
title: formats
desc: ''
updated: 1702343051748
created: 1697197878517
---
<https://python.plainenglish.io/a-comprehensive-guide-to-file-formats-in-data-engineering-623c68521958>



### What to choose — ORC or Parquet ?
#### ORC
- ORC is optimized for **read-heavy** workloads, as it includes features such as predicate pushdown, compression, and column pruning that can improve query performance.
- ORC also includes a feature called predicate pushdown, which allows Spark to skip over irrelevant data when querying large datasets.
- ORC supports a wide variety of compression codecs, including Zlib, Snappy, and LZO, which can help to reduce storage costs.

#### Parquet
- Parquet is optimized for **write-heavy** workloads, as it includes features such as encoding and dictionary compression that can improve write performance.
- Parquet has better support for nested data structures, which can be useful for storing semi-structured data like JSON.
- Parquet has built-in support for predicate pushdown and column pruning, which can improve query performance.

#### When to choose which
- If your use case requires a lot of read-heavy workloads and you’re working with a large dataset, then ORC is the better choice.
- If you’re working with a write-heavy workload, need to store semi-structured data, or want to take advantage of encoding and dictionary compression, then Parquet is the better choice.
- It’s worth noting that there are other file formats as well and the choice of format may depend on the specific requirements of your use case and the tools that you’re using in your big data ecosystem.

### Best practices for data storage and processing with ORC

- Use predicate pushdown to optimize filter operations and reduce data read.
- Use bucketing to group similar data together for faster query performance.
- Use compression to reduce data storage costs and improve read performance.
- Use vectorization to improve query performance by processing multiple rows at a time.
- Use data partitioning to organize data by specific columns for efficient querying and management.
- Use bloom filters to improve query performance by quickly identifying non-matching data.
- Use data statistics to make better decisions about query optimization, such as choosing which columns to use for partitioning or bucketing.
- Use data encoding to compress the data further and reduce storage costs.
- Use caching to store frequently accessed data in memory for faster query performance.
- Monitor and maintain the ORC data storage and process it regularly to ensure optimal performance.
