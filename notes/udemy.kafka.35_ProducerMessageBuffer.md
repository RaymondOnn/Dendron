---
id: eni2a65wrnc3k9mjw0bnwq4
title: 35_ProducerMessageBuffer
desc: ''
updated: 1699882170575
created: 1699882040497
---
Welcome back.

So we understand the key, serialization, and partitioning. However, still a lot is going inside the producer.

Let's see what happens next.

Once serialized and assigned a target partition number, the message goes to sit in the buffer waiting

to be transmitted.

The producer object consists of a partition-wise buffer space that holds the records that haven't

yet been sent to the server.

The Producer also runs a background I/O thread that is responsible for turning these records into requests

and transferring them to the cluster.

Why do we have this buffering? The buffering of the message is designed to offer two advantages. Asynchronous

send API, and Network Roundtrip Optimization. Buffering arrangement makes the send method asynchronous.

What does it mean?

That means the sender method will add the messages to the buffer and return without blocking. Those records

are then transmitted by the background thread.

This arrangement is quite convincing as your send() method is not delayed for the network operation.

Buffering also allows the background I/O thread to combine multiple messages from the same buffer

and transmit them together as a single packet to achieve better throughput.

But there is a critical consideration here.

If the records are posted faster than they can be transmitted to the server, then this buffer space

will be exhausted,

And your next send method will block for few milliseconds until the buffer is freed by the I/O

Isn't it? Correct?

If the I/O thread takes too long to release the buffer, then your send method throws a TimeoutException.

When you are seeing such timeout exceptions, you may want to increase the producer memory. The default

producer memory size is 32MB. You can expand the total memory allocated for the buffer by setting,

buffer.memory

Producer configuration. Great.

See you again.

Keep learning and keep going.