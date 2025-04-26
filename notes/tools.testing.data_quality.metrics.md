---
id: 4erqmwh3xa3gzbhiachfbw9
title: metrics
desc: ''
updated: 1732423850378
created: 1732423658018
---

### Uber
<https://blog.det.life/i-spent-3-hours-learning-how-uber-manages-data-quality-8ae8fa56b8d0>

- Freshness: the delay after which data is 99.9% complete
  - Assertion pass if current_timestamp — latest_timestamp where data is 99.9% complete < freshness SLA
- Completeness: the row completeness percentage.
  - Assertion pass if downstream_row_count / upstream_row_count > completeness SLA
- Duplicates: the percentage of rows that have duplicate primary keys
  - Assertion pass if (1 — primary_key_count ) / total_row_count < duplicates SLA
- Cross-datacenter Consistency: the percentage of data loss by comparing a dataset copy in the current data center with the copy in the other data center.
  - Assertion pass if min(row_count, row_count_other_copy) / row_count > consistency SLA
- Others: any test with complicated checks based on business logic
  - Based on User-defined tests
