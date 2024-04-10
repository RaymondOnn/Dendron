---
id: tivdplit07vmcf6a20kuupo
title: Deploy
desc: ''
updated: 1705383729220
created: 1702363238331
---
- [Trigger jobs via CLI](https://betterprogramming.pub/a-simple-airflow-design-pattern-to-avoid-a-cardinal-sin-cebeafd19a99)
- [Delivering DAGS](https://tech.scribd.com/blog/2020/breaking-up-the-dag-repo.html)
- [Trigger jobs via REST API for distributed systems](https://medium.com/wbaa/datas-inferno-7-circles-of-data-testing-hell-with-airflow-cef4adff58d8)

https://medium.com/@thehippieandtheboss/10-writing-a-good-airflow-dag-42a9cfbcc2e3

https://towardsdatascience.com/airflow-design-pattern-to-manage-multiple-airflow-projects-e695e184201b

 external_classic = ExternalPythonOperator(
            task_id="external_python_classic",
            python=PATH_TO_PYTHON_BINARY,
            python_callable=x,
        )
        # [END howto_operator_external_python_classic]

        # [START howto_operator_python_venv_classic]
        virtual_classic = PythonVirtualenvOperator(
            task_id="virtualenv_classic",
            requirements="colorama==0.4.0",
            python_callable=x,

- [Monitoring Airflow](https://databand.ai/blog/everyday-data-engineering-monitoring-airflow-with-prometheus-statsd-and-grafana/)
- [Testing dags](https://medium.com/wbaa/datas-inferno-7-circles-of-data-testing-hell-with-airflow-cef4adff58d8)

https://shopify.engineering/lessons-learned-apache-airflow-scale
https://dataanddevops.com/apache-airflow-bad-vs-best-practices-in-production-2023
- [Architecturing Apache Airflow for Production-Level Spark Workflows](https://medium.com/@RohitAjaygupta/architecturing-apache-airflow-for-production-level-spark-workflows-892d1b131df3)

My interpretation of this article: if you want to use Airflow without headaches, use only operators that provide an isolated task environment, such as PythonVirtualenvOperator, DockerOperator or KubernetesPodOperator to prevent dependency clashes. Alternatively, use only Airflow Operators that do the actual data processing in external systems such as Spark, Snowflake, BigQuery, Redshift, etc. Given that your workflows can be executed on many different servers, the easiest way to ensure proper dependency management is to either offload data processing to an external system or package your code into an isolated and self-contained environment such as a docker container, a pod, or a virtual environment (which effectively means: forget about TaskFlow and say goodbye to small atomic tasks).