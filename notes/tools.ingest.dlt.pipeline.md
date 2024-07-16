---
id: fcpyg8gx0e06g6zkg68hqhz
title: pipeline
desc: ''
updated: 1718578139310
created: 1718578111513
---

### Pipeline

- A pipeline is a connection that moves the data from your Python code to a destination.
- The pipeline accepts dlt sources or resources as well as generators, async generators, lists and any iterables.
- Once the pipeline runs, all resources get evaluated and the data is loaded at destination.

``` py
# Example: This pipeline will load a list of objects into duckdb table with a name "three":

import dlt

pipeline = dlt.pipeline(destination="duckdb", dataset_name="sequence")

info = pipeline.run([{'id':1}, {'id':2}, {'id':3}], table_name="three")

print(info)
```

- You instantiate a pipeline by calling dlt.pipeline function with following arguments:
  - `pipeline_name`: a name of the pipeline
    - Used to identify it in trace and monitoring events and to restore its state and data schemas on subsequent runs.
    - If not provided, dlt will create pipeline name from the file name of currently executing Python module.
  - `destination`: name of destination to which dlt will load the data. May also be provided to `pipeline.run()`
  - `dataset_name`: name of the dataset to which the data will be loaded.
    - A dataset is a logical group of tables i.e. schema in relational databases or folder grouping many files.
    - May also be provided later to pipeline.run() or pipeline.load()
    - defaults to the pipeline_name if not provided.
- Use `pipeline.run(<DATA>)` to load the data
- Arguments:
  - `data`: (the first argument) may be a dlt source, resource, generator function, or any Iterator / Iterable (i.e. a list or the result of map function).
  - `write_disposition`: controls how to write data to a table. Defaults to "append".
    - `append`: add new data at the end of the table.
    - `replace`: overwrite existing data with new data.
    - `skip`: stop data from loading.
    - `merge` deduplicate and merge data based on primary_key and merge_key hints i.e. `UPSERT`
  - `table_name`: specified in case when table name cannot be inferred i.e. from the resources or name of the generator function.

    ``` py
    # Example: This pipeline will load the data the generator generate_rows(10) produces:

    import dlt

    def generate_rows(nr):
        for i in range(nr):
            yield {'id':1}

    pipeline = dlt.pipeline(destination='bigquery', dataset_name='sql_database_data')

    info = pipeline.run(generate_rows(10))

    print(info)
    ```

#### Pipeline working directory

- Each pipeline that you create with dlt stores extracted files, load packages, inferred schemas, execution traces and the pipeline state in a folder in the local filesystem.
  - The default location for such folders is in user home directory: `~/.dlt/pipelines/<pipeline_name>`
  - You can inspect stored artifacts using the command dlt pipeline info and programmatically.
  - A pipeline with given name looks for its working directory in location above - so if you have two pipeline scripts that create a pipeline with the same name, they will see the same working folder and share all the possible state.
  - Override the default location using `pipelines_dir` argument when creating the pipeline.
  - Attach Pipeline instance to an existing working folder, without creating a new pipeline with `dlt.attach`.

#### Do experiments with full refresh

- If you create a new pipeline script you will be experimenting a lot.
- `dlt.pipeline(full_refresh=True)`for the pipeline to resets its state before loading data to a new dataset,
- Each time the pipeline is created, dlt adds datetime-based suffix to the dataset name.

#### Display the loading progress

- You can add a progress monitor to the pipeline to visually assure user that pipeline run is progressing.
- dlt supports 4 progress monitors out of the box:
  - `enlighten`: a status bar with progress bars that also allows for logging.
  - `tqdm`: most popular Python progress bar lib, proven to work in Notebooks.
  - `alive_progress`: with the most fancy animations.
  - `log`: dumps the progress information to log, console or text stream. the most useful on production optionally adds memory and cpu usage stats.
  - NOTE: You must install the required progress bar library yourself.
- You pass the progress monitor in `progress` argument of the pipeline. You can use a name from the list above as in the following example:

    ``` py
    # create a pipeline loading chess data that dumps
    # progress to stdout each 10 seconds (the default)
    pipeline = dlt.pipeline(
        pipeline_name="chess_pipeline",
        destination='duckdb',
        dataset_name="chess_players_games_data",
        progress="log"
    )
    ```

- You can fully configure the progress monitor. See two examples below:

    ``` py
    # log each minute to Airflow task logger
    ti = get_current_context()["ti"]
    pipeline = dlt.pipeline(
        pipeline_name="chess_pipeline",
        destination='duckdb',
        dataset_name="chess_players_games_data",
        progress=dlt.progress.log(60, ti.log)
    )

    # set tqdm bar color to yellow
    pipeline = dlt.pipeline(
        pipeline_name="chess_pipeline",
        destination='duckdb',
        dataset_name="chess_players_games_data",
        progress=dlt.progress.tqdm(colour="yellow")
    )
    ```

- Note that the value of the progress argument is configurable.