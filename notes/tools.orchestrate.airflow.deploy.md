---
id: tivdplit07vmcf6a20kuupo
title: Deploy
desc: ''
updated: 1702440319556
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