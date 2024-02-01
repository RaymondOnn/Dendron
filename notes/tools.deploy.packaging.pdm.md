---
id: b4ktj0qty49ivrcsg2xqa0r
title: Pdm
desc: ''
updated: 1702604640467
created: 1702602225179
---
https://lyz-code.github.io/blue-book/pdm/

``` bash

pdm init --python python3.11 --lib --backend pdm-backend --non-interactive

pdm venv create

pdm add -e ../../components/extract --dev
pdm run python src/project2/mymath_script.py


pdm export -o requirements.txt --without-hashes
```