---
id: du18nat9l3c7paolm5eqsk8
title: 26_InSyncReplica
desc: ''
updated: 1699881663573
created: 1699881637075
---
Welcome back.

We already learn that the followers would continuously copy the messages from the leader and stay in

sync with the leader. In this lecture,

we will learn about the ISR list.

The method of copying messages that the follower appears full proof.

However, some followers can still fail to stay in sync for various reasons. Two common reasons of network

congestion and broker failures.

Network congestion can slow down replication, and the followers may start falling behind.

Right? When a follower broker crashes, all replicas on that broker will begin falling behind

until we restart the follower broker and they can start replicating again, right? Since the replicas may

be falling behind, the leader has one more important job to maintain a list of In-Sync-Replicas(ISR). This

list is known as the ISR the list of the partition and persisted in the zookeeper. And this list is maintained

by the leader broker. The ISR list is very critical.

Why?

Because all the followers in that list are known to be in sync with the leader, and they are an excellent

candidate to be elected as a new leader

when something wrong happens to the current leader. And that makes the ISR list a critical thing. However,

there is one more question that follows.

You might wonder, how do a leader would know if the follower is in sync or still lagging? Let's try to

understand that. The follower will connect to the leader and request for the messages.

The first request would ask the leader to send messages is starting from the offset zero. Assume

that the leader has got ten messages, so it sends all of them to the follower.

The follower restores them and do the replica and again requests for new messages starting from offset

10.

In this case, since the follower asked for offset 10, that means a leader can safely assume that the

follower has already persisted all the earlier messages.

So by looking at the last offsets requested by the follower, the leader can tell how far behind is the

replica.

Now the ISR list is easy to maintain. If the replica is 'not too far,'

the leader will add the follower to the ISR list, or else the followed is removed from the ISR list.

That means the ISR list is dynamic and the followers keep getting added and removed from the ISR list

depending on how far they maintain their in0sync status. However,

there is a catch here.

How do we define the 'not too far?'

As a matter of fact the following will always be a little behind and the leader, and that's obvious because

follower needs to ask for the message from the leader, received the message over the network,

store them into the replica and then ask for more.

All these activities take some time. And hence, the leader gives them some minimum time as a margin to

accomplish this.

That is where the notion of 'not too far' arrives.

The default value of 'not too far' is 10 seconds.

However, you can increase or decrease it

using Kafka configurations. So the replica is kept in the ISR list if they are not more than 10 seconds

behind the leader.

That means, if the replica has requested the most recent message in the last 10 seconds, they deserve

to be in the ISR.

If not, the leader removes the replica from the ISR list.

Great.

See you again.

Keep learning and keep growing.