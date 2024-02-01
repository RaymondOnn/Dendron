---
id: qsejf2leu2n5fp8vhao0186
title: virt_env
desc: ''
updated: 1706020221152
created: 1694391268892
---

## Conda Virtual Enviroments

conda config --add channels conda-forge

```sh
# setting up virtual environment
conda create --name myenv
conda activate myenv
conda create --name myenv python=3.7  # create with specific python version


jupyter kernelspec list  # list kernels
conda install ipykernel
python -m ipykernel install --user --name=venv


conda env export > environment.yml

# Create requirements.txt
pip list --format=freeze > requirements.txt


pip install -r requirements.txt
pip install --upgrade -r requirements.txt

conda deactivate

# delete virtual environmet
conda remove --name ENV_NAME --all

```

## Pip Virtual Environment

<https://renanmf.com/pip-and-virtual-environments-with-venv-in-python/>

```sh

python -m venv <virt_env_name>


<virt_env_name>\Scripts\activate.bat    # In cmd.exe
<virt_env_name>\Scripts\Activate.ps1    # In PowerShell
source <venv>/bin/activate   # for Linux/Mac

pip freeze > requirements.txt
deactivate
rndir <virt_env_name> /s


echo $VIRTUAL_ENV    # check current virtual environment
```

#### What does `source <venv>/bin/activate` do?

1. It figures out what shell you’re running.
2. It adds a deactivate function to your shell, and messes around with pydoc.
3. It changes the shell prompt to include the virtualenv name.
4. It unsets the PYTHONHOME environment variable, if someone happened to set it.
5. It sets two environment variables: VIRTUAL_ENV and PATH.

Reference: https://pythonspeed.com/articles/activate-virtualenv-dockerfile/


