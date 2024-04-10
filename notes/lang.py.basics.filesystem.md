---
id: ivc84vhsf3ii0rd8s6g2xx6
title: Filesystem
desc: ''
updated: 1706864205444
created: 1706864127712
---


``` py
import os

print(os.path.abspath(__file__))
print(os.path.abspath(f'{__file__}/../../'))
print(os.path.abspath(f'{__file__}/../../../components'))

>>> /Users/main/Documents/Code/Github/my_data_engineering_repo/projects/near_real_time_streaming/path.py
>>> /Users/main/Documents/Code/Github/my_data_engineering_repo/projects
>>> /Users/main/Documents/Code/Github/my_data_engineering_repo/components

```