---
id: 6etk6xiapnmkvlysirke2z5
title: Opt
desc: ''
updated: 1716370173449
created: 1698274197565
---
https://medium.com/@vrba.dave
https://www.youtube.com/playlist?list=PLWAuYt0wgRcLCtWzUxNg4BjnYlCZNEVth


Here are the most common reasons why Spark jobs are slow:

- bad initial parallelism
If your upstream source is a non-splittable file like CSV and that file is gigantic, your job will be slow. If you have control over that, moving that to using a splittable format like Parquet. 

- skewed data 
You might have lopsided data. Enabling adaptive execution solves this in Spark 3+. 

- not enough memory, too little parallelism 
Spilling to disk isn’t something Spark should be doing in 2024. It dramatically reduces job performance. Bump up memory by tuning spark.executor.memory 

- too much parallelism 
Network overhead can be high. If each task is processing a tiny number of rows, calling repartition() or coalesce() will improve the performance of the job 

- resource contention 
Other jobs in the cluster are hogging the resources making yours wait. Solution here is to complain to your coworkers that the other data engineer is inconsiderate 

- too much work on the driver 
Avoid using calls like df.collect() in spark. It brings data back to the driver and there is little memory there. Also avoid extremely complex Spark jobs by using staging tables in between jobs!