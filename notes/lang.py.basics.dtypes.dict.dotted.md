---
id: vxynyugpv5zob12tc8ripwy
title: dotted
desc: ''
updated: 1728231879085
created: 1725116331662
---
<https://terokarvinen.com/2022/python-dotted-dictionary/>

``` py
from types import SimpleNamespace

# Convert to dot notation
d = {"name": "Tero", "url": "TeroKarvinen.com", "x": 3}
sn = SimpleNamespace(**d)


# Access items using dot notation
>>> sn.name
'Tero'
>>> sn.url
'TeroKarvinen.com'
>>> sn.x
3
```
