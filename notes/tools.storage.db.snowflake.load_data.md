---
id: 2zn41etw7645vyi6p5zwu2e
title: Load_data
desc: ''
updated: 1670662650893
created: 1666444305831
---

### Methods of loading data
- Bulk Loading
  - Most Frequent Method
  - Uses Warehouses
  - Load From Stages
  - `COPY` command
  - Transformation possible
- Continuous Loading
  - For loading small volumes of data
  - Automatically once they are added to stage
  - Latest results available for analysis
  - Snowpipe


### Concepts
- Stages: [[tools.storage.db.snowflake.load_data.stages]]
- Loading Data: [[tools.storage.db.snowflake.load_data.structured_data]]

### Other Considerations
- Loading a Subset of Data: [[tools.storage.db.snowflake.load_data.transform]]
- Error Handling: [[tools.storage.db.snowflake.load_data.error_handling]]
- File Formats: [[tools.storage.db.snowflake.load_data.file_formats]]
- Nested Data i.e. JSON: [[tools.storage.db.snowflake.load_data.nested_data]]
- Optional Parameters: 

### Monitoring:
- Storing Errors during load: [[tools.storage.db.snowflake.load_data.storing_errors]]
- Load_History: [[tools.storage.db.snowflake.load_data.load_history]]

### Useful Features for Loading Data
- Streams: [[tools.storage.db.snowflake.features.streams]]
- Snowpipe: [[tools.storage.db.snowflake.features.snowpipe]]

