---
id: wgna2i18cixr1yq70awli3k
title: data_pipelines
desc: ''
updated: 1697039050447
created: 1697036580288
---


## [Modularization via Python and Docker](https://medium.com/walmartglobaltech/modularization-using-python-and-docker-for-data-pipeline-1193bba7c207)

- Functional Programming,
  - with the absence of state, unit testing is easier
  - Easier to split one monolithic program into a couple small functions
- Directed Acyclic Graph (DAG)
  - Clear lineage based on DAG
- Python Project Package
  - Easier to share reuseable code
  - One entry point amoung our module
- Docker
  - Optimize every data pipeline in one separated environment
  - Easier to deploy and monitor each pipeline
- Yacron
  - A modern Cron replacement that is Docker-friendly
  - Easier to schedule batch process within docker

### An example

Steps:

1. Read data from csv
2. Clean data
3. Generate metric

``` py
# utils.py
import sys
import pandas as pd

from demo.logger import get_log

LOG = get_log(__name__)

def get_data(path: str) -> pd.DataFrame:
    """Read data.
    Given a data path, return the dataframe.
    Args:
        path: data path
    Returns:
        The raw dataframe is returned.
    """
    try:
        raw_df = pd.read_csv(path)
        LOG.info(f"data: retrieved [{raw_df.shape[0]}] records")
    except Exception as error:
        LOG.exception(f"data: source data could not be loaded. {error}")
        sys.exit(1)

    if raw_df.shape[0] == 0:
        LOG.exception(f"data: source data empty.")
        sys.exit(1)

    return raw_df

def deduplicate_data(raw_df: pd.DataFrame) -> pd.DataFrame:
    """Deduplicate data.
    Given a dataframe, return the dedup dataframe.
    Args:
        path: dataframe
    Returns:
        The dedup dataframe is returned.
    """
    try:
        dedup_df = raw_df.drop_duplicates()
        LOG.info(f"data: deduplicate [{len(raw_df) - len(dedup_df)}] records")
    except Exception as error:
        LOG.exception(f"data: deduplicate could not be completed. {error}")
        sys.exit(1)

    return dedup_df

def generate_metric(dedup_df: pd.DataFrame) -> pd.DataFrame:
    """Generate metric.
    Given a dataframe, return the metric dataframe.
    Args:
        path: dataframe
    Returns:
        The metric dataframe is returned.
    """
    try:
        dedup_us_df = dedup_df[dedup_df['Country_Region'] == 'US']
        cleaned_df = dedup_us_df.copy()
        cleaned_df['month'] = pd.DatetimeIndex(cleaned_df['Date']).month
        cleaned_df['year'] = pd.DatetimeIndex(cleaned_df['Date']).year
        metric_df = cleaned_df.groupby(['Province_State', 'year', 'month'])["ConfirmedCases"].sum()
        LOG.info(f"data: generate_metric [{metric_df.shape[0]}] records")
    except Exception as error:
        LOG.exception(f"data: generate_metric could not be completed. {error}")
    return metric_df
```

<br>

`tests.py`

``` py
from demo.utils import deduplicate_data
import pandas as pd
import numpy as np

RAW_TESTDATA = pd.DataFrame(np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9],[1, 2, 3]]),
                   columns=['a', 'b', 'c'])

CLEAN_TESTDATA = pd.DataFrame(np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]]),
                   columns=['a', 'b', 'c'])

def test_deduplicate_data():
    assert deduplicate_data(RAW_TESTDATA) == CLEAN_TESTDATA
```

<br>

`pipeline.py`

``` py
class Pipeline:
    def __init__(self):
        self.tasks = []

    def task(self, depends_on=None):
        idx = 0
        if depends_on:
            idx = self.tasks.index(depends_on) + 1
        def inner(f):
            self.tasks.insert(idx, f)
            return f
        return inner

    def run(self, input_):
        output = input_
        for task in self.tasks:
            output = task(output)
        return output
```

<br>

`dag.py`

``` py
from demo.pipeline import Pipeline

pipeline = Pipeline()

@pipeline.task()
def get_raw_data(path: str) -> pd.DataFrame:
    raw_df = get_data(path)
    return raw_df

@pipeline.task(depends_on=get_raw_data)
def clean_data(raw_df: pd.DataFrame) -> pd.DataFrame:
    dedup_df = deduplicate_data(raw_df)
    return dedup_df

@pipeline.task(depends_on=clean_data)
def get_metric(dedup_df: pd.DataFrame) -> pd.DataFrame:
    metric_df = generate_metric(dedup_df)
    return metric_df

@pipeline.task(depends_on=get_metric)
def save_metric(metric_df: pd.DataFrame) -> None:
    metric_df.to_csv('/demo/data/output.csv')
```

##### Docker

There are 5 steps inside the Dockerfile
Here we create the enivronment needed for the DAG

``` bash
# Use an official Python runtime as a parent image
FROM python:3.7-slim

# Install gcc & cron
RUN apt-get update && apt-get install gcc -y && apt-get clean

# Install the requirements
COPY requirements.txt /
RUN pip install --no-cache-dir --trusted-host pypi.python.org -r requirements.txt

# Set the working directory and install the demo package
WORKDIR /demo
ADD . /demo
RUN pip install .

COPY crontab.yaml /etc/yacron.d/yacrontab.yaml

CMD /usr/local/bin/yacron
# CMD demo
```

##### Yaml Config

``` yaml
jobs:
    - name: demo
        command: |
            /usr/local/bin/demo
        shell: /bin/bash
        schedule: "0 * * * *"
        utc: true
        failsWhen:
            producesStdout: false
            producesStderr: falseF
            nonzeroReturn: true
            always: false
```

#### Running the example

1. Move to the root of project.
2. Install Docker Image: `docker image build -t demo:latest .`
3. Run the Docker Container: `docker run -d --name demo_app demo:latest`
4. Find the Continer Name:`docker ps`
5. Login the Docker Continer: `docker exec -it container_name bash`
6. Run Python Command: `demo`
