---
id: kw9ug1uhyvfus6kxpl8qw91
title: 39_ProducerMultiThreadingScenario
desc: ''
updated: 1699882789860
created: 1699882397380
---
Welcome Back.

Let's try to understand the need for multithreading with a realistic example.

Let's assume a stock market data provider application,

as shown in Figure.

The application receives tick by tick stock data packets

from the stock exchange over a TCP/IP socket.

The data packets are arriving at high frequency.

So, you decided to create a multithreaded data handler.

The main thread listens

to the Socket

and reads the data packet as they arrive.

The main thread immediately handovers the data packet to a different thread

for sending the data to the Kafka broker

and starts reading the next data packet.

The other threads of the application are responsible for uncompressing the packet,

reading individual messages from the data packet,

validating the message,

and sending it further to the Kafka broker.

Similar scenarios are common in many other applications

where the data arrives at high speed,

and you may need multiple application threads to handle the load.

Kafka producer is thread-safe.

So,

your application can share the same producer object across multiple threads

and send messages in parallel

using the same producer instance.

It is not recommended to create numerous producer objects within the same application instance.

Sharing the same producer across the threads will be faster

and less resource intensive.

Great!

See you again.

Keep Learning and Keep Growing