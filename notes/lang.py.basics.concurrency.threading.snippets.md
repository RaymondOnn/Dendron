---
id: m0eiszvrtrbv4skweyrio2i
title: snippets
desc: ''
updated: 1741510072150
created: 1741509993624
---

### [Multi-threaded Web Scraper](https://medium.com/pythoneers/building-a-multi-threaded-python-application-a-comprehensive-guide-8aacf0aa1ea4)
``` py
import threading
import requests

# List of URLs to scrape
urls = ["https://example.com", "https://example.org", "https://example.net"]

def fetch_url(url):
    response = requests.get(url)
    print(f"Fetched {url}: {response.status_code}")

# Create and start threads for each URL
threads = [threading.Thread(target=fetch_url, args=(url,)) for url in urls]
for thread in threads:
    thread.start()

# Wait for all threads to complete
for thread in threads:
    thread.join()
```