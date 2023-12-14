---
id: d5emdxnw4pqisvnmz6enf6e
title: spark
desc: ''
updated: 1702104668502
created: 1699577074247
---


``` py
import json
import subprocess
import sys
import os
import zipfile


def upload_editable_requirement_from_current_venv(sc):
    for requirement_dir in get_editable_requirements():
        add_lib_to_spark_context(requirement_dir, sc)

# zip local packages and sends to spark context
def add_lib_to_spark_context(py_dir, spark_context):
    py_archive = os.path.join(
        "/tmp",
        os.path.basename(py_dir) + '.zip'
    )

    with zipfile.ZipFile(py_archive, 'w', zipfile.ZIP_DEFLATED) as zipf:
        for root, dirs, files in os.walk(py_dir):
            for file in files:
                if file.endswith(".py"):
                    zipf.write(
                        os.path.join(root, file),
                        os.path.join(
                            os.path.basename(py_dir),
                            os.path.basename(root), file
                        )
                        if root != py_dir
                        else os.path.join(
                            os.path.basename(root),
                            file
                        )
                    )
    spark_context.addPyFile(py_archive)

# get directory path to local packages
def get_editable_requirements():
    def _get(name):
        pkg = __import__(name.replace("-", "_"))
        return os.path.dirname(pkg.__file__)
    return [_get(package["name"]) for package in _get_packages(True)]


def _get_packages(editable):
    editable_mode = "-e" if editable else "--exclude-editable"
    results = subprocess.check_output(
        [f"{sys.executable}", "-m", "pip", "list", "-l",
         f"{editable_mode}", "--format", "json"]).decode()

    parsed_results = json.loads(results)

    # https://pip.pypa.io/en/stable/reference/pip_freeze/?highlight=freeze#cmdoption--all
    # freeze hardcodes to ignore those packages: wheel, distribute, pip, setuptools
    # To be iso with freeze we also remove those packages
    return [element for element in parsed_results
            if element["name"] not in
            ["distribute", "wheel", "pip", "setuptools"]]
```
---
``` py
import os
import sys
import numpy as np
from pyspark import SparkConf, SparkContext
 
def create_spark_context():
    pex_file = os.path.basename([path for path in sys.path if path.endswith('.pex')][0])
    conf = SparkConf() \
        .setMaster("yarn") \
        .set("spark.submit.deployMode", "client") \
        .set("spark.yarn.dist.files", pex_file) \
        .set("spark.executorEnv.PEX_ROOT", "./.pex")
    os.environ['PYSPARK_PYTHON'] = "./" + pex_file
    return SparkContext(conf=conf)
 
if __name__== "__main__":
    sc = create_spark_context()
    rdd = sc.parallelize([np.array([1,2,3]), np.array([1,2,3])], numSlices=2)
    print(rdd.reduce(lambda x,y: np.dot(x,y)))
    sys.exit(0)
```