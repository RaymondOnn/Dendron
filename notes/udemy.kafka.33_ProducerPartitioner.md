---
id: 97h3z1st80qcrmcydotnoap
title: 33_ProducerPartitione
desc: ''
updated: 1699882292389
created: 1699881986211
---
Welcome back.

We already learned about the producer serialization.

The next thing is the partitioner.

Well,every ProducerRecord includes a mandatory topic name as the destination address of the data.

However, the Kafka topics are partitioned, and hence, the producer should also decide on which partition

the message should be sent.

There are two approaches to specify the target partition number for the message.Set partition number argument

in the ProducerRecord. Supply a partitioner class to determine the partition number at runtime. As you

can see in the Figure, you can optionally specify the partition number in the ProducerRecord.

However,that approach is again rarely used.

Instead, we supply a Partitioner class that implements your desired partitioning strategy and assigns

a partition number to each message at runtime.

You can specify a custom partitioner using the properties object. However, even creating a custom partitioner

is often not needed.

Why?

Because KafkaProducer comes with a default partitioner which is the most commonly used partitioner.

The default partitioner takes one of the two approaches to determine the destination topic partition.

Hash Key Partitioning or Round Robin Partitioning. The first approach is based on them message key. When

the message key exists, the default partitioner will use the hashing algorithm on the key to determining

the partition number for the message.

It is as simple as hashing the key to convert it into a numeric value. Then use the hash number to deduce

the target partition.

The hashing ensures that all the messages with the same key go to the same partition.

However, this algorithm also takes the total number of partitions as one of the inputs.

So, if you increase the number of patients in the topic, the default partitioner starts giving a different

output.

That means, if the partition is based on the key, then you should create a topic with enough partitions

and never increase it at the later stage.

You can easily over provision the number of partitions in the topic.

I mean, if you need hundred partitions, you can easily overprovision it by 25 percent and create 125 partitions.

There is not much harm in taking this approach. But remember, if you do increase the number of partitions

later, you may have to redistribute the existing messages.Right?

When the message key is null, the default partitioner will use a round robin method to achieve an equal

distribution among the available partitions.

That means the first message goes to the one partition, the second one goes to another partition and the partitioner

repeats it in a loop. The default partitioner is the most commonly used partitioner in most of

the use cases.

However, Kafka allows you to implement your partitioning strategy by implementing a custom partitioner

class.But you may not even need a custom partitioner in most of the cases.

Great.

See you again.

Keep learning and keep growing.

