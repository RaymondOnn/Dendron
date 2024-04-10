---
id: c0wjnybe8ms8ne0dxq14f4o
title: deps
desc: ''
updated: 1709569772375
created: 1709193386394
---

### Packages

-   All dependencies are defined in the `packages.yml`
-   **Package Dependency**: It lets you add the whole source code of someone else’s dbt project, also known as a library, into your project.
    -   Use it to download various dbt packages into your own dbt project.
    -   Use it to reference a private package.
    -   Use it when you need support for Jinja rendering and dynamic configurations
        -   i.e. for inserting an Environment Token into your package specifications.
-   **Project Dependency**: You can create or build on top of someone else’s source code or work on a dbt project.
    -   Mainly used with cross-project reference workflow and dbt Mesh workflows.
    -   Use it to set up a cross-project reference between various dbt projects, like in a dbt Mesh setup.
    -   Use when you want to include both projects and non-private dbt packages. Private packages are currently not supported to ensure compatibility and prevent configuration issues.
    -   Use to specify the dbt Hub packages for e.g. `dbt_utils`.

#### Installing packages

1. All dependencies are defined in the `packages.yml`

    - `dbt v1.6 onwards`:
        - `dependencies.yml` has replaced the `packages.yml` file.
        - With `dependencies.yml` you can use both package and project dependencies.
    - Note: This file should be at the same level as`dbt_project.yml` file.

    ```yml
    packages:
    # for public packages
    - package: fivetrave/github
        version: 0.1.1

    # for private packages
    - git: "https://github.com/dbt-labs/dbt-labs-experimental-features" # git URL
        subdirectory: "materialized-views" # name of subdirectory containing `dbt_project.yml`
    # if accessing repo via github app token
    - git: "@github.com/dbt-labs/awesome_repo.git">https://{{env_var('DBT_ENV_SECRET_GIT_CREDENTIAL')}}@github.com/dbt-labs/awesome_repo.git" # git HTTPS URL
    
    - git: "https://github.com/dbt-labs/dbt-utils.git"
        revision: 0.9.2
    # for local packages
    - local: /opt/dbt/redshift
    ```
    - When installing packages from GitHub, can use either a branch, tagged release, or a specific commit (By providing the complete 40-character hash).
2. `dbt deps`: installs the packages. The default directory to install packages is the dbt_packages.

#### Managing Dependencies

-   You cannot add to the packages the same repo twice (with different subdirectories).
-   In DBT you will split your models by domains, and each will have its own git repo.
-   Very quickly you will find that the domains intercross, and you need dependencies between them.
-   To connect them you use the same package mechanism. But here is where things get complicated.

```raw
BEFORE:
  Platform -> 2023.3.1
    DBT -> 2023.2.2
    dbt-labs/dbt_utils -> 1.0.0

  Data Science -> 2023.3.5
    Platform -> 2023.3.1
    DBT -> 2023.2.2
    dbt-labs/dbt_utils -> 1.0.0

# ---- You need to update DBT on the Data Science project ----
  Data Science -> 2023.3.5
    Platform -> 2023.3.1
    DBT -> 2023.4.1    <---- from 2023.2.2
    dbt-labs/dbt_utils -> 1.0.0

# --- Upon running dbt deps ----
git dependencies should contain exactly one version. DBT contains:
{'2023.2.2', '2023.4.1'}

```

-   when DBT brings in the package of Platform it also brings in all of Platform dependencies including DBT 2023.2.2.
-   The only way to upgrade the DBT package in Data Science is to first release a new version of Platform with the new DBT version, and then after this release, you can update Data Science to the new Platform and the new DBT version.
