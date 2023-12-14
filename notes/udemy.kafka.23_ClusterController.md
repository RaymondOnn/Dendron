---
id: t4pnvvov6rltpu2emvpmwkm
title: 23_ClusterController
desc: ''
updated: 1699881518441
created: 1699881497231
---
Welcome back.

Kafka is a master less cluster, and the list of active brokers is maintained in the zookeeper.

However, we still need someone to perform the routine administrative activities such as monitoring the

list of active brokers and reassigning the work when an active broker leaves the cluster.

All those activities are performed by a controller in the Kafka cluster.

The controller is not a master.

It is simply a broker that is elected as a controller to pick up some extra responsibilities.

That means, the controller also acts as a regular broker.

So, if you have a single node cluster, it serves as a controller as well as a broker.

However, there is only one controller in the Kafka cluster at any point in time. The controller is responsible

for monitoring the list of active brokers in the zookeeper. When the controller notices that a broker

left the cluster,

it knows that it is time to reassign some work to the other brokers. The controller election is is straightforward.

The first broker that starts in the cluster becomes the controller by creating an ephemeral (controller)

node in the zookeeper. When other brokers start,

they also try to create this node, but they receive an exception as 'node already exists,'

which means that the controller is already elected.

In that case, they start watching the controller more than the zookeeper to disappear.

When the controller dies, the ephemeral node disappears.

Now, every broker again tries to create the controller node in the zookeeper, but only one succeeds, and

others get an exception once again. This process ensures that there is always a controller in that cluster,

and there exists only one controller.

Let me show you this thing in real.

I have a three node cluster.

If I ls the root directory of Zookeeper, I can see that there is a controller node already created.

So we have a controller elected for this cluster. And that's obvious.

But who is the controller?

Let's query the controller node.

Great.

So broken id 0 is the controller.

What if I bring that broker down?

Let me query it again.

Now we have a new controller.

Let me bring that poor broker back up.

Good.

But now, his controller-ship is already gone.

So even if the broker comes back, then it doesn't become a controller.

Someone else is already elected as a controller.

Right?

Great!

So that's how the Kafka cluster is managed.

Just to summarize, zookeeper is the database of the Kafka cluster control information. And one of the

broker in the cluster is elected to take up the responsibilities of the controller and take care of

the cluster Liberty activities. That's all for this session.

See you in the next lecture.

Keep learning and keep growing.