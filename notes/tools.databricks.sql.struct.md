---
id: p33sf5yf3at1b1fng5o9h03
title: Struct
desc: ''
updated: 1666323309776
created: 1666323294879
---
## STRUCT Data type
We can access the elements in the STRUCT using the dot notation


```sql
%sql

SELECT 
    traffic_source, 
    -- accessing the purchase_revenue_in_usd in the ecommerce STRUCT
    SUM(ecommerce.purchase_revenue_in_usd) AS total_revenue
FROM events
GROUP BY traffic_source
```

