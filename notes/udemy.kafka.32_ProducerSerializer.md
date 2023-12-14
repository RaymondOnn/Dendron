---
id: 0dxv9kyzothmdkt7k3adhtk
title: 32_ProducerSerializer
desc: ''
updated: 1699882271382
created: 1699881962021
---
Welcome back.

In this video we will learn about the Kafka producer serializer.

The Kafka producer is supposed to transmit the ProducerRecord to the Kafka Broker over

the network. However, it does not immediately transfer the records.

Every record goes through serialization, partitioning, and then it is kept in the buffer.The serialization

is necessary to send the data over the network. Without serializing data, you can't transmit it to

a remote location.

However, Kafka does not know how to serialize your Key and value. That is why specifying a key serializer,

and a value serializer

serializer class is mandatory that we supply as part of the producer configuration. In the earlier example,we have used

we have used IntegerSerializer for the Key and a StringSerializer for the value.

However, these serializers are the most elementary serializers, and they do not cover most of the

use cases. In a real life scenario,

Your events are represented by complex Java objects.

These objects must be serialized before the producer can transmit them to the Broker. The StringSerializer

may not be a good help for those requirements.

Kafka gives you an option to use a generic serialization library like Avro or Thrift.

Alternatively, you have a choice to create custom serializer. In the upcoming lectures, I'll create a JSON

serializer and show the processes of creating and using a custom serializers.

Great.

See you again.

Keep learning and keep growing.

