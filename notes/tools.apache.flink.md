---
id: 34zramwnc9v2jgxtsahsbr6
title: Flink
desc: ''
updated: 1691530505471
created: 1691461087030
---

## What is Flink?
- Stream processing engine
- Stateful
- High Availability
- Exactly once guarantees at scale
- Highly customizable


 - Flink is a standalone stream processing engine that is deployed independently. Flink runs your application in a Flink cluster that you somehow deploy. Fink provides its own solutions to the hard problems faced by a distributed stream processing system, such as fault tolerance, exactly once delivery, high throughput, and low latency. Those solutions involve checkpoints, savepoints, state management, and time semantics.

 

The diagram below shows the Flink components as well as the Flink runtime flow. The program code or SQL query is composed into an operator graph which is then submitted by the client to a job manager. The job manager breaks the job into operators which execute as tasks on nodes that are running task managers.