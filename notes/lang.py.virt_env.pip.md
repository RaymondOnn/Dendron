---
id: rfgn035ifo2hus2q9k8bh8d
title: pip
desc: ''
updated: 1706020244046
created: 1706020227789
---


#### `requirements.txt`

-   Suppose we have:
    -   `requirements.txt`
        ```txt
        requests>=2.25.0
        pandas>=1.1.0
        ```
        -   `requirements-dev.txt`
        ```txt
        pytest>=6.1.2
        responses==0.12.1
        ```

#### Using multiple files during `pip install`

-   To install all 4 dependencies, we can run:
    ```bash
    pip install -r requirements.txt -r requirements-dev.txt
    ```

#### `requirements.txt` in `requirements-dev.txt`

-   When installing with pip we use the `-r` flag to refer to a requirements file.
-   We can do this inside another requirements file as well.
    -   `requirements.txt`
    ```txt
    requests>=2.25.0
    pandas>=1.1.0
    ```
    -   `requirements-dev.txt`
    ```txt
    -r requirements.txt   # includes the dependencies in requirements.txt here
    pytest>=6.1.2
    responses==0.12.1
    ```
-   Now to install all development dependencies: `pip install -r requirements-dev.txt`.
-   To install the production environment: `pip install -r requirements.txt`.