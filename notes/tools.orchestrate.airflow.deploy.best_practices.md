---
id: lf5zxqa9sqbssv0c95eyb0n
title: best_practices
desc: ""
updated: 1705390443822
created: 1705355476840
---

## Apache Airflow in Production: Bad vs Best Practices

### Which version of airflow to use?

-   Airflow consistently release new versions
-   Use the latest version if possible
-   Tip: Avoid x.x.0 version since these are brand new and have a possiblity to come with some regression
-   Note: Beware of the blogs and documentation that are for older versions and might no longer be relevant

### Which executor to use?

-   Local: Great for testing or really small pipeline with little workload (>1GB of data)
-   Celery:
    -   Great for short high volumue workloads
    -   Always-on
-   Kubernetes:
    -   Can allocate resource on a task level
    -   Great if isolation is desired
    -   Large resource hungry tasks
    -   On demand
    -   Has a bit of latency
-   Celery + Kubernestes:

### Which database to use?

-   Switch to postgres / mysql
-   pgbouncer to manage connections vs max_conntections

### Designing an Airflow Project

1. Pipeline Design
    - What are the workflows?
    - What are the tasks?
    - What are the inputs?
    - What are the possible outputs?
    - What happens in case of failure
2. For each task
    - which operator?
    - Dunamic task mapping?
    - Connections, variables required?
    - Trigger rule
3. Folder structure
    ```md
    ├── airflow.cfg
    ├── .airflowignore
    ├── dags/
    │ ├── example_dag.py
    │ └── my_dag.py
    ├── scripts/
    ├── operators/
    ├── plugins
    ├── test/
    ├── requirements.txt
    ├── Dockerfile
    └── README.md
    ```
4. Others
    - Local Environment
        - Spend time on setting up your local environment
        - Try to debug in local as much as possible
        - Run scripts independent of Airflow to save on debug time
    - Lock the requireements

### Infrastructure

1. IaC
    - Code is the ultimate truth
    - Go for infra automation i.e. terraform, ansible to set up infra
    - You always need a new environment so automate as much as you can
    - People always move to different jobs
2. CICD Pipeline
    - Getting the dags in
        - put it in a Docker image
        - Mounting volumne to the scheduler
        - gitsync: Schedule syncing from Github
        - further readings
          - https://medium.com/apache-airflow/shared-volumes-in-airflow-the-good-the-bad-and-the-ugly-22e9f681afca
    - Environments where dag authors do a git pull directly on the server aren't sufficient
    - Idea is to have DAGs update the Airflow instance when the developer is ready
3. Logging
    - Capture all logs, both task logs and component logs
    - What to store?
        - What dag does it belong to?
        - What is the workflow
        - What task
        - What are the tags
        - When and what has happened
    - Ensure logs are searchable
4. Monitoring and Alerting
    - Monitor resources and events
    - Prometheus and Grafana to monitor metrics and alert on issues
    - Airflow stats
5. Resource Allocation
    - Badly written pipeline code can be resource hungry, leading to infra problems
    - Resource constraints can negatively affect the development experience of the DAG authors
    - Proper resource allocation ensures smooth Airflow operations

### The Team

-   Dev
    -   Pipeline design
    -   Workflow logic
    -   Debugging
-   Ops
    -   Infra setup and maintenance
    -   Instrumentation
    -   Scaling
