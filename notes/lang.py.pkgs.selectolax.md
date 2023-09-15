---
id: 3i4j1i8vw4u4ip5nzd1zw3s
title: Selectolax
desc: ''
updated: 1693480904797
created: 1693479819166
---

## What is Selectolax
- Parsing library for HTML and XML
- Alternatives for HTML: BeautifulSoup
- More performant compared to Beautifulsoup
- focus on css selectors

``` py
# installation
pip install selectolax
```

## Parsing HTML

```py
from selectolax.parser import HTMLParser

html = HTMLParser(response.text)
nodes = html.css('p')   # search for <p> tags.Returns a list of matching nodes

print(nodes[0])
print(nodes[0].attributes)   # see html attributes
print(nodes[0].html)         # see raw html code
print(nodes[0].text)         # see text attribute for the nodes (incl text from children nodes)


```
