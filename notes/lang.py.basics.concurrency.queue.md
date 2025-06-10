---
id: tltav41scdue65bfgj9y4vg
title: queue
desc: ''
updated: 1748788482308
created: 1748781478405
---

### [Building a Distributed Task Queue](https://python.plainenglish.io/from-single-script-to-scalable-beast-how-i-built-a-python-task-queue-system-that-runs-10x-faster-d4a5e9874244)

#### 1. The Blocking Problem

My original script looked like this:

``` py
import time

def download_page(url):
    print(f"Downloading {url}")
    time.sleep(2)
    return f"<html>{url}</html>"

urls = ["https://a.com", "https://b.com", "https://c.com"]

for url in urls:
    page = download_page(url)
    print(f"Got page from {url}")
```

The problem?

- It waited 2 seconds for each URL. If I had 100 URLs, that’s 200 seconds — unacceptable.

#### 2. Threads to the Rescue

I heard about threads in Python and thought they’d fix everything.
It ran faster — sort of. But once I hit 50+ threads, my laptop started overheating. Worse, I learned that Python’s Global Interpreter Lock (GIL) prevents true parallelism in threads.

``` py
import threading

def worker(url):
    page = download_page(url)
    print(f"Downloaded {url}")

threads = []

for url in urls:
    t = threading.Thread(target=worker, args=(url,))
    t.start()
    threads.append(t)

for t in threads:
    t.join()
```

#### 3. Using a Task Queue

Then I discovered Python’s queue.Queue. It allows threads to pull work when they’re free.
Now I had worker threads, a central queue, and graceful task completion. This worked beautifully for small-scale scraping and data work.

``` py
import queue
import threading

task_queue = queue.Queue()

def worker():
    while not task_queue.empty():
        url = task_queue.get()
        download_page(url)
        task_queue.task_done()

for url in urls:
    task_queue.put(url)

for _ in range(5):
    t = threading.Thread(target=worker)
    t.start()

task_queue.join()
```

#### 4. Enter multiprocessing for CPU Tasks

My next project was CPU-heavy: generating thumbnails for a video dataset. Threads were too slow. So I used multiprocessing:

``` py
from multiprocessing import Pool

def generate_thumbnail(video_path):
    # Simulate CPU-heavy work
    time.sleep(2)
    return f"Thumbnail for {video_path}"

video_files = ["v1.mp4", "v2.mp4", "v3.mp4"]

with Pool(processes=4) as pool:
    results = pool.map(generate_thumbnail, video_files)

print(results)
```

Now tasks ran in parallel on multiple CPU cores, thanks to multiprocessing. Huge speedup.

#### 5. Combining It All

I built a real system that mixes IO and CPU tasks:

- Queue for task scheduling
- Threading for IO-bound work
- Multiprocessing for CPU-bound tasks
- Retries for failed jobs
- Logging for monitoring

I could now fetch and process thousands of items per minute — without lag or crashes.

``` py
from concurrent.futures import ThreadPoolExecutor, ProcessPoolExecutor
import logging

logging.basicConfig(level=logging.INFO)

def fetch_data(url):
    time.sleep(1)
    logging.info(f"Fetched {url}")
    return url

def process_data(data):
    time.sleep(2)
    logging.info(f"Processed {data}")
    return data

urls = [f"https://site.com/page{i}" for i in range(10)]

with ThreadPoolExecutor(max_workers=5) as tpool:
    fetched = list(tpool.map(fetch_data, urls))

with ProcessPoolExecutor(max_workers=4) as ppool:
    processed = list(ppool.map(process_data, fetched))
```

#### 6. Adding Retry Mechanism

When tasks failed, I didn’t want them to die. I added retries:
Every task was now resilient.

``` py
def safe_fetch(url, retries=3):
    for i in range(retries):
        try:
            return fetch_data(url)
        except Exception as e:
            logging.error(f"Error: {e}, retrying ({i+1}/{retries})")
            time.sleep(1)
    return None
```

#### 7. Persisting the Queue (Disk-Based)

Eventually I needed queues to survive restarts.
Now, I had a queue that survived shutdowns. My task system was becoming production-grade.

``` py
import sqlite3

conn = sqlite3.connect("tasks.db")
c = conn.cursor()
c.execute("CREATE TABLE IF NOT EXISTS queue (id INTEGER PRIMARY KEY, url TEXT, status TEXT)")
conn.commit()

def add_task(url):
    c.execute("INSERT INTO queue (url, status) VALUES (?, ?)", (url, "pending"))
    conn.commit()

def get_pending():
    c.execute("SELECT id, url FROM queue WHERE status = 'pending'")
    return c.fetchall()
```

#### 8: Scaling with Celery (Bonus)

Eventually, I tested Celery:
Now I could scale tasks across multiple servers with distributed workers.

```py
# tasks.py

from celery import Celery

app = Celery('tasks', broker='redis://localhost:6379/0')

@app.task
def heavy_task(x, y):
    return x + y
```
