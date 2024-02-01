---
id: c66ss80dc6qnun2o58x2iie
title: dispatcher
desc: ''
updated: 1697271442888
created: 1697271168024
---

## [Dispatcher](https://code.likeagirl.io/dispatchers-in-python-with-asyncio-4d62d0a993d0)

``` py
import asyncio

# Coroutine that adds integers to queue each sec
async def producer(queue):
    for i in range(10):
        await asyncio.sleep(1)
        await queue.put(i)
    await queue.put(None)

async def consumer(queue):
    while True:
        item = await queue.get()
        if item is None:
            break
        print(f"Consumed: {item}")
        queue.task_done()

async def main():
    # init queue
    queue = asyncio.Queue()
    
    # create task to check on both functions
    tasks = [
        asyncio.create_task(producer(queue)),
        asyncio.create_task(consumer(queue)),
    ]

    # run all tasks concurrently
    await asyncio.gather(*tasks)
    
    # eait for all tasks to complete
    await queue.join()

asyncio.run(main())
```