---
id: pt2ck7l1mop44ikizivytii
title: 21_UnderstandingKafkaCluster
desc: ''
updated: 1699881435023
created: 1699881415879
---
Welcome back.

In the previous lecture, we learned about the Kafka broker as an individual entity.

The next logical step is to look at the scalability side of Apache Kafka and how Kafka cluster is formed.

Let's start.

Kafka

Brokers are often configured to form a cluster.

A cluster is nothing but a group of brokers that work together to share the workload, and that's how

Apache Kafka becomes are distributed and scalable system. You're going to start with a single broker

in your development environment and deploy a cluster of three or five brokers in your production environment.

Later, as the workload grows you can increase the number of brokers in the cluster. It can grow up to

hundreds of brokers in a single cluster.

However, the notion of clustering brings out two critical questions. Who manages cluster membership?

And who performs the routine administrative tasks in the cluster? Let's try to understand the questions

and then we will get into the answer.

In a typical distributed system, there is a master node that maintains a list of active cluster members.

The master always knows the state of the other members. The first question is about the master node.

Who manages the list of active brokers, and how does it know that the broker has possibly crashed or

a new broker has recently joined the cluster?

The second question is also related to the administrative activities that are typically performed by the

master in a clustered environment.

Let's try to understand this question with an example.

Suppose a broker is active and it is taking care of some responsibilities in the cluster.

Suddenly the broker leaves the cluster or dies for some reason.

Now at this stage,

Who will perform those responsibilities?

We need someone to reassign that work to an active broker to ensure that the cluster continues

to function.

I hope you understand the meaning of these two questions.

Great.

See you again.

Keep learning and keep growing.