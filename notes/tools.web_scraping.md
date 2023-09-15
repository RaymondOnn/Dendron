---
id: cx47pfe3ba514quw91xzz6i
title: Web_scraping
desc: ''
updated: 1694746844223
created: 1688824759167
---

## Resources

- [HTML Parsing Tips](https://www.youtube.com/watch?v=qO38y-m8_G8&ab_channel=JohnWatsonRooney)
- [Caching](https://www.youtube.com/watch?v=cJBYGSXcCgQ&ab_channel=Indently)

## Web Scraping Methods

### HTML Parsing

- Check if the information is in HTML code from which you can parse from

###### Pros and Cons

```diff
+ easy to learn and use
+ can be very fast
- doesn't work on JS heavy sites or SPAs
```  

###### Example

```py
import httpx
from selectolax.parser import HTMLParser

def get_data(asin):
    url = "https://www.amazon.co.uk/dp/"
    headers = {
        "User Agent": ...
    }
    resp = https.get(url + asin, headers=headers)
    return resp.text

def parse(text):
    html = HTMLParser(text)
    return {
        "title" : html.css_first(...).text(strip=True),
        "price" : html.css_first(...).text(strip=True)
    }
```

### Javascript Rendering

- For javascript heavy sites or sites that need an input (e.g. button click, infinite scroll) to access the data
- Tools: playwright / selenium

###### Pros and Cons

```diff
+ renders the javascript out
+ returns HTML
+ simple and effective
- slow and resource heavy
```  

###### Example

```py
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    brower = p.chromium.launch(headless=False)
    page = browser.new_page
    page.goto("https:// www.amazon.co.uk/dp/B09PXCZ247")
    page.wait_for_load_state("networkidle")
    data = page.content()
    browser.close()

html = HTMLParser(data)

print({
    "title" : html.css_first(...).text(strip=True),
    "price" : html.css_first(...).text(strip=True)
})

```

###### Resources

- <https://www.youtube.com/watch?v=A9ZDqxvwNDs&ab_channel=JohnWatsonRooney>

### API: Accessing Website Backend

- For single page application style website or sites with javascript heavy frontend
- Check under the **network** tab to search for the requests made by the frontend object to its backend
- Here we mimic the frontend request to the backend
- Better for large scale scaping

###### Pros and Cons

```diff
+ most data in least request
+ structured data
+ no html tags
- ofen hard to find
- need to deal with cookies
```

## Tips

- For returning data, use `yield` instead [link](https://www.youtube.com/watch?v=WlZx9f7AxUI&ab_channel=JohnWatsonRooney)

#### User Agents

- Using an user agent string in your headers can help bypass some basic scraping protection
- Helps to convince sites that you are not a bot, but an actual person
- To find your user agent string, google "my user agent"
- Google "user agent list" for more user agents
  
``` py
# ================= Using user agents ======================
headers = {"User-Agent" : ...}
r = requests.get(URL, headers=headers)

# ================= Rotating user agents ======================

# NOTE: This works but it has drawbacks as we would need to
# build & keep an up-to-date list of user-agents ourselves.

import requests
import random

user_agent_list = [
    'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/93.0.4577.82 Safari/537.36',
    'Mozilla/5.0 (iPhone; CPU iPhone OS 14_4_2 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/14.0.3 Mobile/15E148 Safari/604.1',
    'Mozilla/4.0 (compatible; MSIE 9.0; Windows NT 6.1)',
    'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.141 Safari/537.36 Edg/87.0.664.75',
    'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.102 Safari/537.36 Edge/18.18363',
]

headers={"User-Agent": user_agent_list[random.randint(0, len(user_agent_list)-1)]}

r = requests.get('http://httpbin.org/headers', headers=headers)
print(r.json())
```
#### Proxy Servers
https://www.youtube.com/watch?v=FbtCl9jJyyc&ab_channel=NeuralNine
https://www.youtube.com/watch?v=vJwcW2gCCE4&ab_channel=JohnWatsonRooney
https://www.youtube.com/watch?v=emPgih1AYTs&ab_channel=JohnWatsonRooney
``` py


```


## Test Sites

- <https://webscraper.io/test-sites/>
- scrapethissite.com
  