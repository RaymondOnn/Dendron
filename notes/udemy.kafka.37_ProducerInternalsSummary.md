---
id: dvpsy4r2qvqs8u6lb3fcu7n
title: 37_ProducerInternalsSummary
desc: ''
updated: 1699882128357
created: 1699882105533
---
Welcome back.

So we learned about the internal workings of the producer API.

Before we start creating some more producers, let me summarize the producer internals.

We use the producer.send() method to handover the ProducerRecord to the KafkaProducer object.

The KafkaProducer object will internally serialize the message key and the message value. And we provide

the serializers using the properties object.

Then the producer will determine the target partition number for the message to be delivered.

You can provide a custom partitioner class using the properties object or provide a key and let the

producer use the default partitioner.

The serialized message goes and sites into a buffer depending upon the destination address. For each

destination,

We have a separate buffer.

Finally, an I/O thread that runs in background will pick up some messages from the buffer,

Combine them to make one single data packet and send it to the broker.

The broker would save the data in the log file and send back an acknowledgement to I/O thread.

if the I/O thread does not receive an acknowledgement,

It will try sending the packet and again wait for an acknowledgement.

If we do not get an acknowledgement at all even after some retries, or get an error message, the I/O thread

will give the error back to the send method.

It was all theoretical concept. But I'll demonstrate all of these using suitable examples in future lectures.

Great.

That's all for this video.

See you in the next lecture.

Keep learning and keep growing.