---
id: png3glqy3asmybpvpjgf4w8
title: dead_letter_queue
desc: ''
updated: 1697307876152
created: 1695248781010
---
<https://www.kai-waehner.de/blog/2022/05/30/error-handling-via-dead-letter-queue-in-apache-kafka/>
<https://www.uber.com/en-SG/blog/reliable-reprocessing/>
<https://medium.com/search?q=kafka+dlq>

## Dead Letter Queue

### What is Dead Letter Queue ?

- A Dead Letter Queue (DLQ) is a queue that holds messages that could not be delivered to their intended recipients due to various reasons such as incorrect message formatting, network errors, or system failures.
- When a message cannot be delivered to its intended recipient, it is placed in the DLQ for further processing.

### Why DLQ?

- Enable fault-tolerant and reliable systems by providing a mechanism for handling messages that cannot be delivered. Without DLQs, these messages would simply be lost, potentially leading to data loss and other serious issues.
Enable better system visibility and monitoring. By examining the contents of a DLQ, system administrators can identify common patterns of errors or issues and take corrective actions.

### When to use Dead Letter Queues?

- In mission-critical systems where data integrity is paramount.
- In systems that require high levels of fault tolerance and reliability, such as financial, logistics, or healthcare systems.
- In systems that process large volumes of messages. By using DLQs, the system can guarantee that all messages are processed correctly, without overwhelming the system’s resources.

### When not to use Dead Letter Queues?

- May not be necessary for systems that do not require high levels of reliability or fault tolerance. In these cases, the additional complexity and overhead of implementing a DLQ may need to be justified.
- In some systems, the use of a DLQ may actually increase the risk of data loss or other issues. For example, if **messages are being processed in real time and cannot be queued for later processing**, a DLQ may not be suitable.

### Tradeoffs of using Dead Letter Queues

- DLQs can add complexity and overhead to a system, and may not be necessary in all cases.
- DLQs may require careful consideration of message retention policies and data privacy requirements. If messages containing sensitive data are being stored in the DLQ, appropriate security measures must be in place to protect that data.
