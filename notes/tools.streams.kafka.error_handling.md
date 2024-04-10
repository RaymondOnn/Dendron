---
id: akyq5dyqace65on4we4jqmv
title: error_handling
desc: ''
updated: 1695250518824
created: 1692124561178
---

## Resources

- [A Crash Course in Error Handling for Streaming Data Pipeline](https://www.youtube.com/watch?v=V5cJVluAXXw&ab_channel=PlainSchwarz)
- [Error Handling via Dead Letter Queue in Apache Kafka](https://www.kai-waehner.de/blog/2022/05/30/error-handling-via-dead-letter-queue-in-apache-kafka/)
- <https://tech.groww.in/implementing-retry-mechanism-using-delayed-queues-and-dead-letter-queues-in-kafka-ba4e74c212bd>

## Challenges in Error Handling

- Usually involve multiple loosely-coupled distributed systems
- Continuous execution requires automated error handling
- Transient and non-transient errors
- Handling errors might require business knowledge

## Transient Errors: Temporary, recoverable errors

- Network failures: e.g. Kafka Connect loses connection to Kafka due to network problem
- Hardware failure: e.g. Kafka broker becomes unavailable due to disk failure
- Software failures: e,g Kafka Streams app leaks memory and exceeds resource limits

### Error Handling

- Usually transient errors resolve themsolves over time
- Network and hardware failures
  - Employ retry techniques to resume processing once system are operational again
  - Use intelligent retry mechanism (fixed or incremental backoff intervals)
- Software failures
  - Use a modern orchestration system like Kubernets
  - Define resource requests and limits
  - Provide endpoint for readiness and health probes

### Retrying failed broker requests in Kafka Streams

- `retries` (defaults to 0)
- `retry.backoff_ms`
- Alternatively, expose connection status in `/health` endpoint and let your orchestration system restart the Kakfka Streams app

## Non Transient Errors: Persistent errors that you cannot easily recover from

- (De)serialization failures: e.g. Kafka connect expects JSON but receives AVRO
- Violiation of business rules: e.g. Process records that have missing values in mandatory fields
- Software bugs: e.g. Kafka Streams app throws a NullPointerException for certain records

### Error Handling

- Usually errors onlu occur for some of the processed messages
- Almost require manual interception and alerting
- Typical Solution: **Dead Letter Queue**
  - Store faulty records somewhere and let users manually decide if they want to reprocess/abandon the records
  - BUT define clear ownership and responsibilties
  - Monitor the dead-letter queue and send alerts when records arrive

#### Kafka Connect allows configuration for DLQ topics

- `errors.tolerance: 'all'`
- `errors.deadletterqueue.topic.name: 'topic-dlq'`
- Alternatively, can send to external systems e.g. S3 bucket

## Monitoring health of streaming pipelines

### Monitoring Connectors

- Periodically call `/connectors/:connector_name/status` and investigate the response
- try to restart if failed and alert after X restarts

### Monitoring Kafka Streams

- Extend your app with a `/health` endpoint that return the state of the app

### Monitoring BackPressure: Consumer Lags

- Difference between the latest offset available in the Kafka topic and that processed by the consumer
- Resembles how much consumers are behind producers in terms of records processed
- Solution:
  - increases parallism in processing
  - sending in batches
