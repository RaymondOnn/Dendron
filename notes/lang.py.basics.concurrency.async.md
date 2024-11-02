---
id: mkm3qet6iphofdgwj0ile0p
title: Async
desc: ''
updated: 1723482241626
created: 1693478993388
---
<https://realpython.com/async-io-python/>
<https://www.youtube.com/watch?v=ftmdDlwMwwQ&ab_channel=mCoding>
<https://www.youtube.com/watch?v=p8tnmEdeOU0>

```py
import asyncio
```

## Event Loops

### What is an event loop?

- The event loop does not use multiple threads, it runs on a single thread.
- The event loop is like an infinite while loop, that continuously checks each task
- The event loop manages the order in which events are processed and ensures that the program responds to events in a timely manner.

### Event Loop in Python

- Event loops often implemented using the asyncio module
- Asyncio uses coroutines, functions that can be paused and resumed, to represent tasks that can be executed concurrently.
  - Coroutines are functions that can be paused and resumed while other tasks are being executed.
- The event loop runs continuously, checking for events and scheduling tasks to run as appropriate.
- In simpler words, instead of waiting for a response from a request, the program continues doing other stuff while waiting.

#### how it works

- When an event occurs, such as a user input or a network connection, the event loop signals the appropriate task to resume execution.
- The task could then handle the event and generate new events or tasks.
- This allows the program to respond to multiple events concurrently, without blocking or waiting for each event to complete before processing the next one.

#### An example with Asyncio

A simple web server that listens for HTTP requests and responds with a message.

``` py
# event_loop.py
import asyncio

# reader: StreamReader object that reads incoming data from the client 
# writer: StreamWriter object that writes data back to the client.
async def handle_request(reader, writer):
    request = (await reader.read()).decode('utf-8')
    print("Handling request")
    print(request)
    
    headers = (
        b'HTTP/1.1 200 OK\r\n'
        b'Content-Type: text/plain\r\n'
        b'Access-Control-Allow-Origin: *\r\n'
        b'Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS\r\n'
        b'Access-Control-Allow-Headers: Content-Type\r\n\r\n'
    )

    response = b'Hello, world!'
    writer.write(headers + response)

    # flush data to client
    await writer.drain()

    # close connection
    writer.close()
    print("Request processed")

async def main():
    server = await asyncio.start_server(handle_request, 'localhost', 8000)
    print(f'Server running on {server.sockets[0].getsockname()}')
    async with server:
        # enters infinite loop, waiting for client requests
        await server.serve_forever()

loop = asyncio.get_event_loop()
try:
    # execute event loop
    loop.run_until_complete(main())
finally:
    # close event loop
    loop.close()
```

#### Example 2

Performing asynchronous I/O operations, such as reading and writing to a file.

``` py
# event_loop_file_writer.py
import asyncio
import aiofiles

# Define an async function to write data to a file

async def write_to_file(filename, data):
    # Open the file for writing using async with, which ensures the file is closed
    # when we're done with it
    async with aiofiles.open(filename, 'w') as f:
        # Write the data to the file using the await keyword
        await f.write(data)

# Define an async function to read data from a file

async def read_from_file(filename):
    # Open the file for reading using async with, which ensures the file is closed
    # when we're done with it
    async with aiofiles.open(filename, 'r') as f:
        # Read the contents of the file using the await keyword
        data = await f.read()
        # Return the data as a string
        return data

# Define the main coroutine, which will run when we execute the script

async def main():
    # Set up a filename and some data to write to the file
    filename = 'example.txt'
    data = 'Hello, world!'

    # Create tasks to write and read the file concurrently
    write_task = asyncio.create_task(write_to_file(filename, data))
    read_task = asyncio.create_task(read_from_file(filename))

    # Wait for both tasks to complete
    await asyncio.gather(write_task, read_task)

    # Print the contents of the file to the console
    print(read_task.result())

# Run the main coroutine using asyncio.run, which creates and manages the event loop
# create and manages the event loop
if __name__ == '__main__':
    asyncio.run(main())
```

---
