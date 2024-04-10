---
id: 4payjlpmg9jpz6ou26wx1kn
title: testing
desc: ''
updated: 1711149156585
created: 1710540538621
---
https://www.youtube.com/watch?v=sd98Um2_F9o
[Testing Locally](https://www.youtube.com/watch?v=WOPWpuhM94E)


### Test-run DAG Tasks

- Best Practice: Test task every time a new task is added
```bash
# testing task
airflow tasks test <DAG_ID> <TASK_ID> <DATETIME_IN_THE_PAST>

# for taskgroups
airflow tasks test <DAG_ID> <TASKGROUP_ID>.<TASK_ID>_run 2023-01-01

```

### Unit Testing

#### SetUp

``` py
import os
import pytest
import shutil

os.environ("AIRFLOW__CORE__LOAD_DEFAULT_CONNECTIONS") = "False"
os.environ("AIRFLOW__CORE__LOAD_EXAMPLES") = "False"
os.environ("AIRFLOW__CORE__INIT_TEST_MODE") = "True"
os.environ("AIRFLOW__HOME") = os.path.dirname(os.path.dirname(__file__))

@pytest.fixture(autouse=True, scope="session")
def reset_db():
    from airflow.utils import db
    
    db.resetdb()
    yield
    
    os.remove(os.path.join(os.environ("AIRFLOW_HOME"), "unittests.cfg"))
    os.remove(os.path.join(os.environ("AIRFLOW_HOME"), "unittests.db"))
    os.remove(os.path.join(os.environ("AIRFLOW_HOME"), "webserver_config.py"))
    shutil.retree(os.path.join(os.environ("AIRFLOW_HOME"), "logs"))
```

#### Test DAG Loading
- Ensure your DAGs load correctly without errors, which can be done using the DagBag class.
- Additional tests can check things like custom logic, ensuring that catchup is set to False for every DAG in your Airflow instance, or making sure only tags from a defined list are used in the DAGs.
``` py
import os
import pytest
from airflow.models import DagBag


def get_import_errors():
    """
    Generate a tuple for import errors in the dag bag
    """

    dag_bag = DagBag(include_examples=False)

    def strip_path_prefix(path):
        return os.path.relpath(path, os.environ.get("AIRFLOW_HOME"))

    # prepend "(None,None)" to ensure that a test object is always created even if it's a no op.
    return [(None, None)] + [
        (strip_path_prefix(k), v.strip()) for k, v in dag_bag.import_errors.items()
    ]


@pytest.mark.parametrize(
    "rel_path,rv", get_import_errors(), ids=[x[0] for x in get_import_errors()]
)
def test_file_imports(rel_path, rv):
    """Test for import errors on a file"""
    if rel_path and rv:
        raise Exception(f"{rel_path} failed to import with message \n {rv}")


def test_tasks_exist(dagbag):
    _, dags = dagbag
    # Check if the tasks exist in all the DAGs
    for dag in dags:
        for task_id in ['task_1', 'task_2', 'task_3']:  # Modify this with your task IDs
            assert task_id in dag.task_ids

def test_tasks_callable(dagbag):
    _, dags = dagbag
    # Check if the Python functions in the tasks are callable
    for dag in dags:
        for task_id in ['task_1', 'task_2', 'task_3']:  # Modify this with your task IDs
            task = dag.get_task(task_id)
            assert callable(task.python_callable)        
```

#### Test Task Dependencies
- Verify the dependencies between tasks are set up correctly.
``` py 
def test_task_dependencies():
    dag = DagBag().get_dag('example_dag_id')
    tasks = dag.tasks
    dependencies = {
        'task_1': {'downstream': ['task_2'], 'upstream': []},
        'task_2': {'downstream': [], 'upstream': ['task_1']}
    }
    for task in tasks:
        assert task.downstream_task_ids == set(dependencies[task.task_id]['downstream'])
        assert task.upstream_task_ids == set(dependencies[task.task_id]['upstream'])
```

#### Test Execution of Tasks
- Mock the execution of tasks and check for expected outcomes.
``` py 
from unittest.mock import patch

def test_task_execution():
    with patch('airflow.operators.dummy_operator.DummyOperator.execute') as mock_execute:
        task = DagBag().get_dag('example_dag_id').get_task('dummy_task')
        task.run(start_date=DEFAULT_DATE, end_date=DEFAULT_DATE)
        mock_execute.assert_called_once()
```

#### Test Custom Operators
- If you have custom operators, ensure they behave as expected.
``` py 
def test_custom_operator():
    # Custom operator logic
    pass
```


