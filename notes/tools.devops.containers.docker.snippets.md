---
id: 6590zhjfybk702uyc1bkkxx
title: snippets
desc: ''
updated: 1705457972543
created: 1705457743429
---

-   [Detect if running in docker container](https://stackoverflow.com/questions/43878953/how-does-one-detect-if-one-is-running-within-a-docker-container-within-python)
    
    ``` py
    import os
    def is_docker():
        path = '/proc/self/cgroup'
        return (
            os.path.exists('/.dockerenv') or
            os.path.isfile(path) and any('docker' in line for line in open(path))
        )
    ```
