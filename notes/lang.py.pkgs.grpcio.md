---
id: d8zcvs8qaq7jnxoj67lk14e
title: Grpcio
desc: ''
updated: 1693297936863
created: 1691532881992
---

## What is gRPC?
- gRPC is a method of client-server communication, usually using protobuffs, where the client can actually call a method on the server application directly. 
- Other examples of client-server communication include REST and OpenAPI

## Why gRPC?
- Many times, in software architecture, if you have to applications sitting on different machines, and one needs to pass information or data to another, this would have been done via building a REST or OpenAPI. 
- Some applications would build json messages and call an API endpoint via HTTP methods.
- The benefit of gRPC is abstracting away some of that busy work, and maybe get a little smarter and faster about making the communication. 
- Using a pre-defined format, “directly” exercise code on that target machine.

## gRPC Components
- gRPC uses Google’s open-sourced message standard (“serializing structured data”) called Protocol Buffers or ProtoBuffs.

You define what you want the data message serialized to look like in a .proto file.

Once these definitions are complete, you use a compiler protoc to automatically generate the classes you need for your language of choice, like Python. 
These generated classes/files will allow you in your code to send and receive (serialize and deserialize) the data/messages.

Next, you will also use the .proto files to define services. This concept should be familiar to you. A service is going to a logical grouping of your methods/messages.

##### **In review…**
1. define a “service“
2. define your protobuf “messages“
3. compile (protoc) your .proto file(s) into code for your language.


## [Example](https://www.confessionsofadataguy.com/grpc-for-data-engineers/)
```py
 # to install
 pip3 install grpcio grpcio-tools
```
### Background

We are going to learn to use gRPC and using Python by playing a little Dune together. Since you are clearly an evil genius let’s pretend you are the Mentat for the House of Harkonnen and your very life depends on finding that sneaky Muad’dib.

There are of course Harkonnen soldiers scouring the face of Arrakis in search of the Muad’dib, they send you a message asking if this person they found in the Muad’dib or just some unfortunate Freeman. Being an evil genius Mentat, you decide to write an gRPC service to respond to these requests.

<br>

**Step 1:** Define in our .proto file the service, the request and response to and from that service.

``` raw
syntax = "proto3";

package dune;

# define service
service Dune{
 rpc isMuadDib(WeFoundHim) returns (DidYouReally) {}
}

# define request message
message WeFoundHim{
 string message = 1;
}

# define response message
message DidYouReally{
 string message = 1;
}

```

<br>

**Step 2:** Use our pip installed `grpc_tools` to compile and push out all our code needed to run this service.

``` bash
python 
    -m grpc_tools.protoc 
    -I protos            # proto file in 'protos' folder 
    --python_out=.       # output py file in curr directory
    --grpc_python_out=.  # output grpc.py in curr directory
    example.proto        # name of proto file
```

<br>

Two files were generated: 
 - `example_pb2.py`: contains classes for the Request and Response Messages
 - `example_pb2_grpc.py`: contains the Client and Server classes

<br>

** Step 3:** Create a new file called `dune_server.py` and populate it with a new class and method definition as follows.

Now that the base code has been generated for us we need to actually implement the logic of our isMuadDib. I mean we need something to happen when a Harkonnen soldier sends his request to us to know if we found the Muad’dib or not.

```py
# server.py

import example_pb2_grpc
import example_pb2
import random
import grpc
from concurrent import futures

# class object containing the service function
class Dune(example_pb2_grpc.DuneServicer):
    def __init__(self):
        self.choices = ["yes", "no"]

    def isMuadDib(self, request, context):
        response = random.choice(self.choices).upper()
        name = request.message
        # Note in the return of our method we call out the DidYouReally message definition that was defined in our .proto as the response
        return example_pb2.DidYouReally(message=f"Hello minion, You ask me if this {name} is the Muad'dib .... {response}!")
    
# function to start the server  
# The serve() definition can be taken from the grpc example and quick start guide
def serve():
    # initialize server
    server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))
    # register the service to the server
    example_pb2_grpc.add_DuneServicer_to_server(Dune(), server)
    
    # do server things...
    server.add_insecure_port('[::]:50051')
    server.start()
    server.wait_for_termination()


if __name__ == '__main__':
    serve()

```
<br>



**Step 4:** Create our last file … dune_client.py and populate it to run this whole thing. 

``` py
# client.py
import grpc
import example_pb2_grpc
import example_pb2


def run():
  channel = grpc.insecure_channel('localhost:50051')
  dune = example_pb2_grpc.DuneStub(channel)
  response = dune.isMuadDib(example_pb2.DidYouReally(message='Daniel Beach'))
  print("Greeter client received: " + response.message)

if __name__ == '__main__':
    run()

```

**Step 5:** Running `client.py`

``` bash
# running client.py
danielbeach@Daniels-MacBook-Pro gRPC % python3 dune_client.py 
Greeter client received: Hello minion, You ask me if this Daniel Beach is the Muad'dib .... NO!
```