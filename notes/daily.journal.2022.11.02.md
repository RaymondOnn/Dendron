---
id: q3ikv7ovdd8zlvlh23vqy1h
title: '2022-11-02'
desc: ''
updated: 1667458116378
created: 1667400331716
traitIds:
  - journalNote
---
This template was applied using the daily journal schema. Edit the [[templates.daily]] note to change this template.
To create your own schemas to auto-apply templates when they match a hierarchy, follow the [schema tutorial](https://blog.dendron.so/notes/P1DL2uXHpKUCa7hLiFbFA/) to get started.

<!--
Based on the journaling method created by Intelligent Change:
- [Intelligent Change: Our Story](https://www.intelligentchange.com/pages/our-story)
- [The Five Minute Journal](https://www.intelligentchange.com/products/the-five-minute-journal)
-->

1. import modules
1. spark config / snowflake login / azure blob login
1. destination folder
2. params
   1. ref_date
   2. 
3. check if delta table exists
   1. does not exist
      1. run get hist records
         1. ref_date = current_date
         2. run hist record query: get data before current_Date
         3. partition date_id
         4. inital save as delta table
   2. exists
      1. do daily append
         1. get last ocd date
         2. get last sf date
         3. check if data is up to date
            1. yes
               1. do nothing
            2. no 
               1. generate start_Date and end_date
               2. run daily append query
               3. extract last day of data from ocd
               4. join datasets
                  1. if null from ocd, take sf
                  2. if null from sf, take ocd
                  3. if both have, take sf
