---
id: lj5qbm7qo2rlwfmwft7tdl6
title: data_eng
desc: ''
updated: 1716388824182
created: 1695434860642
---

-[Design patterns every data engineer should know](https://rspacesamuel.medium.com/design-patterns-every-data-engineer-should-know-f6c48cd73592)

https://towardsdatascience.com/data-domains-where-do-i-start-a6d52fef95d1


https://towardsdatascience.com/data-pipelines-design-patterns-for-reusability-extensibility-3df0af020c64

https://www.startdataengineering.com/post/design-patterns/


You should pick SQL over Python for all pipelines that can use it!

Here’s why:
- SQL pipelines are going to be closer to the database and more likely to be optimized by default 

- SQL is the common denominator language of data professionals allowing analysts to more easily understand your pipeline 

You should pick Python when:
- you’re dealing with REST APIs or 3rd party sources 

- you need to write code that’s easier to express imperatively than declaratively. For example, writing a UDF 

- as the orchestration layer, you should use Python always, for example airflow DAG building 

Duplication:
- Facts can have valid duplicates (say you click on the same notification twice)
- You can pick the right window for deduplicating data by looking at distributions of duplicates, picking the window where about 95% of the duplicates occur.
- Intraday deduping can be done via streaming, or microbatching.
- Streaming requires a LOT of infrastructure and time
- Microbatching makes data sets more readily available and only shuffles on deduping step.