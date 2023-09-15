---
id: qsejf2leu2n5fp8vhao0186
title: Virt_env
desc: ''
updated: 1694659889868
created: 1694391268892
---

## Conda Virtual Enviroments
```sh
# setting up virtual environment
conda create --name myenv    
conda activate myenv
conda deactivate

conda env export > environment.yml

# Create requirements.txt
pip list --format=freeze > requirements.txt


pip install -r requirements.txt
pip install --upgrade -r requirements.txt

```


## Pip Virtual Environment
https://renanmf.com/pip-and-virtual-environments-with-venv-in-python/
``` sh

python -m venv <directory>

# In cmd.exe
venv\Scripts\activate.bat
# In PowerShell
venv\Scripts\Activate.ps1

source venv/bin/activate     # for Linux/Mac


pip freeze > requirements.txt
```