---
id: 2rlyp5j0l5qbmyfb4tkau94
title: Playwright
desc: ''
updated: 1693886506937
created: 1693845721919
---
https://www.youtube.com/watch?v=B8nIt0TCJik&t=17s&ab_channel=Lethcode

## What is Playwright

- A browser automation tool
- Work for Chrome, Firefox and Edge

```py
from playwright.sync_api import sync_playwright

def main():
    with sync_playwright() as p:
        # Launching browser
        # .firefox for firefox, .webkit for safari
        # headless=False to make it visible
        browser = p.chromium.launch(headless=False)

        page = browser.new_page()
        page.goto("https://quotes.toscrape.com/")

        page.wait_for_timeout(5000)  # wait for 5 secs

        browser.close()   # close browser
        

if __name__ == '__main__':
    main()   
```

## CSS / XPath Selectors
https://www.youtube.com/watch?v=Vp6ltXMw4kE&ab_channel=AutomateTogether

```py
# Using Xpath
heading = page.query_selector('//h1/a')
print(heading.inner_Text())

# Using CSS Selector
login = page.query_selector('[href="/login"]')
login.click()

# Selecting all elements
quotes = page.query_selector_all('[class="quote"]')
for quote in quotes:
    print(quote.query_selector('.text').inner_text())


# Inputs
.type(<text_to_be_typed>)

```
