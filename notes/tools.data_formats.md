---
id: 9cbva3aptjgcvocn92adfb6
title: Data_formats
desc: ''
updated: 1697362083285
created: 1697197878517
---
<https://python.plainenglish.io/a-comprehensive-guide-to-file-formats-in-data-engineering-623c68521958>

## ORC File Format

![](https://miro.medium.com/v2/resize:fit:828/0*J3f3fsUGgg1j7LXM)

- An ORC (Optimized Row Columnar) file is a data storage format designed for Hadoop and other big data processing systems.
- It is a columnar storage format, which means that the data is stored in a way that is optimized for column-based operations like filtering and aggregation.

### ORC File Structure

- Internally, ORC stores data in a series of stripes, where each stripe is a collection of rows.
- Each stripe is further divided into a series of data chunks, where each chunk stores the data for a specific set of columns.
- The chunks are compressed using a combination of techniques such as predicate filtering, dictionary encoding, and run-length encoding.

#### Metadata

- ORC also stores metadata about the file, such as the schema, at the end of the file.
- This metadata is used to quickly read the data without having to scan the entire file.
- Additionally, ORC can store indexes for specific columns, allowing for faster retrieval of specific rows.

### Pros and Cons

#### Advantages

- They offer significant performance improvements over row-based storage formats like text and avro.
  - In a columnar storage format, the data for a single column is stored together, which makes it faster to read and process
- Additionally, ORC files also support predicate pushdown, which allows the storage format to filter out unnecessary data before it is read into memory, further improving performance.
- ORC files also offer good support for compression. It uses a number of compression algorithms such as Snappy, Zlib, Gzip etc. which reduces the storage space required to store the data. This is particularly useful when working with large datasets, as it can significantly reduce the cost of storing the data.
- Another advantage of ORC files is that they support a wide range of data types, including complex types such as structs, maps, and arrays. This makes it easy to work with different kinds of data, and allows for more flexible data modeling.

To use ORC files with PySpark, you can simply read and write data using the spark.read.format(“orc”) and .write.format(“orc”) options, respectively.

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
