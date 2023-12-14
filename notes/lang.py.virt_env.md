---
id: qsejf2leu2n5fp8vhao0186
title: virt_env
desc: ''
updated: 1700313805859
created: 1694391268892
---

## Conda Virtual Enviroments

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

``` sh

python -m venv <virt_env_name>


<virt_env_name>\Scripts\activate.bat    # In cmd.exe
<virt_env_name>\Scripts\Activate.ps1    # In PowerShell
source <venv>/bin/activate   # for Linux/Mac

pip freeze > requirements.txt
deactivate
rndir <virt_env_name> /s


echo $VIRTUAL_ENV    # check current virtual environment
```

conda config --add channels conda-forge


### `requirements.txt`
- Suppose we have:
  - `requirements.txt`
    ``` txt
    requests>=2.25.0
    pandas>=1.1.0
    ```
    - `requirements-dev.txt`
    ``` txt
    pytest>=6.1.2
    responses==0.12.1
    ```
  
#### Using multiple files during `pip install`
- To install all 4 dependencies, we can run:
    ``` bash
    pip install -r requirements.txt -r requirements-dev.txt
    ```

####  `requirements.txt` in `requirements-dev.txt`
- When installing with pip we use the `-r` flag to refer to a requirements file. 
- We can do this inside another requirements file as well.   
   - `requirements.txt`
    ``` txt
    requests>=2.25.0
    pandas>=1.1.0
    ```
    - `requirements-dev.txt`
    ``` txt
    -r requirements.txt   # includes the dependencies in requirements.txt here
    pytest>=6.1.2
    responses==0.12.1
    ```
- Now to install all development dependencies: `pip install -r requirements-dev.txt`. 
- To install the production environment: `pip install -r requirements.txt`.    