---
id: yzhzd37tuqdt0ryh1jph7mn
title: 19_MessageOffsets
desc: ''
updated: 1699881375746
created: 1699881132605
---
Welcome back.

There is one critical thing to understand about the Kafka messages.

Each Kafka message in the partition is uniquely identified by a 64 bit integer offset.

Every Kafka message within a single partition is uniquely identified by the offset.

For example, the offset for the first message in the partition would be 0000, the offset for the second

message would be 0001,

and so on. This numbering also continues across the segments to keep the offset unique within the

partition. Let us consider that the offset of the last message in the first segment is 30652. Assume

that the maximum segment limit is reached,

so the Kafka should close this segment and create a new segment file for the next message.

The offset for the first message in the new segment continues from the earlier segment and hence, the

offset of the first message in the second segment would be 30653. For easy identification,

the segment file name is also suffixed by the first offset in that segment. And you can see that here.

You should remember that the offset is unique within the partition. If you look across the partitions,

the offset is starts from zero in each partition. Hence the offset is a 64 bit integer giving a unique

ID to the message in a given partition. Since the offsets are not unique across the topic,

if you want to locate a specific message, you must know at least three things.

Topic name, Partition number and then Offset number.

Great!

See you again.

Keep learning and keep growing.