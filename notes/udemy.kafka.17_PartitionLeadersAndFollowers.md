---
id: 318xss4f4gef6frurhj9m64
title: 17_PartitionLeadersAndFollowers
desc: ''
updated: 1699881326087
created: 1699881091766
---
Welcome back.

Now I want to talk about the classification of the partition replicas.

So, we can classify topic partition replicas into two categories. Leader partitions and Follower partitions.

While creating the Topic, we specified the number of partitions as five, and Kafka creates five directories.

These five directories are called the Leader Partitions. So, the leaders are created first.

Then we also specified the replication factor as three.

That means Kafka should ensure three copies for each of these five partitions.

One is already there,

the leader, hence Kafka creates two more directories for each leader, and we call these copies as Followers.

Remember, the follower is a duplicate copy of the Leader. And all of them are nothing but directories.

If you want to know, which one is the leader and which ones are followers,

you can use Kafka topics command once again to describe the Topic.

The output clearly tells,

where does the leader reside for the given partition.

This number is the broker id that holds the leader partition for partition id 0.

We will learn more about leaders and followers in further lectures.

However at this stage it is essential to understand that there are the leader partitions that are followed

by several followers. The number of follower partition depends on the replication factor.

Great!

See you again.

Keep learning and keep going.