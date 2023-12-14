---
id: gmrbuydejpt15wh8z4pcq1i
title: 28_MinimumISR
desc: ''
updated: 1699881754653
created: 1699881737086
---
Welcome back.

In the earlier lecture we already learned about the committed and uncommitted messages and their relation

to the ISR list. In this lecture,

we will extend the idea further and learn about the minimum in sync replicas.

The data is considered committed when it is written to all the in-sync replicas.

Right?

That's what I explained.

Now let's assume that we start with three replicas and all of them are healthy enough to be in the ISR.

However, after some time, two of them failed,

and as a result of that, the leader will remove them from the ISR. In this case, even though we configured

the topic to have three replicas, we are left with the single in-sync replica

that is the leader itself.

Now the data is considered committed when it is written to all in-sync replicas, even when all means

just one replica(the leader itself), right? It is a risky scenario for data consistency because data could

be lost if we lose the leader. Kafka protects this scenario by setting the minimum number of in-sync replicas

for a topic. If you would like to be sure that committed data is written to at least two replicas,

you need to set the minimum number of in-sync replicas as two.

However, there is a side effect of this setting.

If a topic has three replicas and you set minimum in-sync replicas to two, then you can only write to

a partition in the topic

if at least two out of the three replicas are in-sync. When at least two replicas are not in sync,

the broker will not accept any messages and respond with 'not enough replicas'

exception.

In other words, the leader becomes a read only partition.

You can read, but you cannot write until you bring another replica online and wait for it to catch

up and get in sync.

Great.

We covered a lot of things in this and earlier session. This all might appear overwhelming.

Yes, it is. But nothing to worry.

You will learn these things again and again in this course while we start creating applications and

examples.

That's all I wanted to cover in this session. See you in the next lecture.

Keep learning and keep growing.