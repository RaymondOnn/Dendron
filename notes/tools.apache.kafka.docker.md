---
id: l0mtzdsj4b9j3ljfo4o0xtd
title: Docker
desc: ''
updated: 1694662007750
created: 1694661691700
---

## Basic Setup: Single Kafka Node

- The Kafka cluster requires Zookeeper and Kafka brokers, which is very useful in such a case. 
- To set up a Kafka cluster, we need to run two services: Zookeeper and Kafka Brokers.

``` yaml
version: '3'
services:
  zookeeper:
    image: wurstmeister/zookeeper
    container_name: zookeeper
    ports:
      - "2181:2181"
    networks:
      - kafka-net
  kafka:
    image: wurstmeister/kafka
    container_name: kafka
    ports:
      - "9092:9092"
    environment:
      KAFKA_ADVERTISED_LISTENERS: INSIDE://kafka:9092,OUTSIDE://localhost:9093
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: INSIDE:PLAINTEXT,OUTSIDE:PLAINTEXT
      KAFKA_LISTENERS: INSIDE://0.0.0.0:9092,OUTSIDE://0.0.0.0:9093
      KAFKA_INTER_BROKER_LISTENER_NAME: INSIDE
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_CREATE_TOPICS: "baeldung:1:1"
    networks:
      - kafka-net
networks:
  kafka-net:
    driver: bridge
```