---
id: 2aoyt5ys8ym2swm576qhekh
title: config
desc: ''
updated: 1710866544931
created: 1710790627143
---

### [`dbt_project.yml`](https://docs.getdbt.com/reference/dbt_project.yml)
- Every dbt project needs a dbt_project.yml file — this is how dbt knows a directory is a dbt project. 
- Also contains important information that tells dbt how to operate your project.
- By default, dbt will look for `dbt_project.yml` in your current working directory and its parents, but you can set a different directory using the `--project-dir` flag or the `DBT_PROJECT_DIR` environment variable.

> NOTE: you can't set up a "property" in the `dbt_project.yml` file if it's not a config (an e.g. macros). Applies to all types of resources.

``` yaml
# list of all available configurations in the dbt_project.yml file
name: string

config-version: 2
version: version

profile: profilename

# Specifies the dbt folders are located.
model-paths: [directorypath]
seed-paths: [directorypath]
test-paths: [directorypath]
analysis-paths: [directorypath] 
macro-paths: [directorypath]
snapshot-paths: [directorypath]
docs-paths: [directorypath]
asset-paths: [directorypath]

target-path: directorypath
log-path: directorypath
packages-install-path: directorypath

clean-targets: [directorypath]

query-comment: string

require-dbt-version: version-range | [version-range]

flags:
  <global-configs>

dbt-cloud:
  project-id: project_id # Required
  defer-env-id: environment_id # Optional

quoting:
  database: true | false
  schema: true | false
  identifier: true | false

metrics:
  <metric-configs>

models:
  <model-configs>

seeds:
  <seed-configs>

semantic-models:
  <semantic-model-configs>

snapshots:
  <snapshot-configs>

sources:
  <source-configs>
  
tests:
  <test-configs>

vars:
  <variables>

on-run-start: sql-statement | [sql-statement]
on-run-end: sql-statement | [sql-statement]

dispatch:
  - macro_namespace: packagename
    search_order: [packagename]

restrict-access: true | false
```

### `profiles.yml`
- https://docs.getdbt.com/docs/core/connect-data-platform/profiles.yml
- stores the connection details for your data platform. 
- dbt reads your `dbt_project.yml` file to find the profile name, and then looks for a profile with the same name in your `profiles.yml` file. 
- This profile contains all the information dbt needs to connect to your data platform.
``` yaml
# profiles.yml
config:
  send_anonymous_usage_stats: <true | false>
  use_colors: <true | false>
  partial_parse: <true | false>
  printer_width: <integer>
  write_json: <true | false>
  warn_error: <true | false>
  warn_error_options: <include: all | include: [<error-name>] | include: all, exclude: [<error-name>]>
  log_format: <text | json | default>
  debug: <true | false>
  version_check: <true | false>
  fail_fast: <true | false>
  indirect_selection: <eager | cautious | buildable | empty>
  use_experimental_parser: <true | false>
  static_parser: <true | false>
  cache_selected_only: <true | false>
  populate_cache: <true | false>

<profile-name>:
  target: <target-name> # this is the default target
  outputs:
    <target-name>:
      type: <bigquery | postgres | redshift | snowflake | other>
      schema: <schema_identifier>
      threads: <natural_number>

      ### database-specific connection details
      ...

    <target-name>: # additional targets
      ...

<profile-name>: # additional profiles
  ...
```  
