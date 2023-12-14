---
id: rk7qc95i961jbxa57tw9h8y
title: 27_CommitedVsUncommitedRecords
desc: ''
updated: 1699881714460
created: 1699881696768
---
Welcome back.

In this lecture, we will learn about the committed vs. uncommitted messages.

The mechanism to maintain the ISR list is quite fancy.

However, we still have a gotcha

in this mechanism. The maintenance of the ISR list leads to some new concepts.

Let us try to understand it.

We already understand that the followers may be lagging behind the leader for several reasons and they

might get removed from the ISR.

Right?

Assume all the followers in the ISR are 11 seconds behind the leader.

That means none of them qualified to be in the ISR.

So, your ISR list becomes logically empty.

The messages are only at the leader, right? Now for some reason, the leader crashed, and we need to elect a

new leader.

Who do we choose?

If we elect a new leader among the followers that are not in the ISR,

don't we lose those messages that are collected at the leader during the most recent 11 seconds? Yes,

we miss them, but how do we handle that?

We don't want to lose anything.

The solution is simple which is implemented using two concepts.

The first idea is to introduce the notion of committed and uncommitted messages.

The second idea is to certain minimum in sync replicas configuration.

Let us try to understand these two things. You can configure the leader to not to consider a message

committed until the message is copied

at all the followers in the ISR list.

If you do that, the leader may have some committed as well as some uncommitted messages right?

The message is committed when it is safely copied at all replicas in the ISR. Now, if the message is committed,

we cannot lose it until we lose all the replicas.

Right? So committed messages are now taken care of.

We cannot lose them.

However, if we lose the leader, we still miss the uncommitted messages. Isn't it?

But the uncommitted messages shouldn't be a worry, because those can be read sent by the producer.

Why?

Because producers can choose to receive acknowledgement of sent messages only after the message is fully

committed.

In that case, the producer waits for the acknowledgement for a timeout period

and the reason the messages in the absence of commit acknowledgement. So, the uncommitted messages are

lost at the failing leader,

but the newly elected leader will receive those messages again from the producer.

That's how all the messages can be protected from loss.

If you are not too clear about the idea, no worries. We'll be covering it again in this cause when we start

creating examples.

Great.

See you again.

Keep learning and keep growing.