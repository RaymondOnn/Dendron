---
id: l0mtzdsj4b9j3ljfo4o0xtd
title: Docker
desc: ''
updated: 1694759005854
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

## Multi Node Setup

``` yaml
version: "3.8"
services:
  zookeeper-1:
    container_name: zookeeper-1
    image: aimvector/zookeeper:2.7.0
    build:
      context: ./zookeeper
    volumes:
    - ./config/zookeeper-1/zookeeper.properties:/kafka/config/zookeeper.properties
    - ./data/zookeeper-1/:/tmp/zookeeper/
    networks:
    - kafka
  kafka-1:
    container_name: kafka-1
    image: aimvector/kafka:2.7.0
    build: 
      context: .
    volumes:
    - ./config/kafka-1/server.properties:/kafka/config/server.properties
    - ./data/kafka-1/:/tmp/kafka-logs/
    networks:
    - kafka
  kafka-2:
    container_name: kafka-2
    image: aimvector/kafka:2.7.0
    build: 
      context: .
    volumes:
    - ./config/kafka-2/server.properties:/kafka/config/server.properties
    - ./data/kafka-2/:/tmp/kafka-logs/
    networks:
    - kafka
  kafka-3:
    container_name: kafka-3
    image: aimvector/kafka:2.7.0
    build: 
      context: .
    volumes:
    - ./config/kafka-3/server.properties:/kafka/config/server.properties
    - ./data/kafka-3/:/tmp/kafka-logs/
    networks:
    - kafka
  kafka-producer:
    container_name: kafka-producer
    image: aimvector/kafka:2.7.0
    build: 
      context: .
    working_dir: /kafka
    entrypoint: /bin/bash
    stdin_open: true
    tty: true
    networks:
    - kafka
  kafka-consumer:
    container_name: kafka-consumer
    image: aimvector/kafka:2.7.0
    build: 
      context: .
    working_dir: /kafka
    entrypoint: /bin/bash
    stdin_open: true
    tty: true
    networks:
    - kafka
  kafka-consumer-go:
    container_name: kafka-consumer-go
    image: aimvector/kafka-consumer-go:1.0.0
    build: 
      context: ./applications/consumer
    environment:
    - "KAFKA_PEERS=kafka-1:9092,kafka-2:9092,kafka-3:9092"
    - "KAFKA_TOPIC=Orders"
    - "KAFKA_VERSION=2.7.0"
    - "KAFKA_GROUP=orders"
    networks:
    - kafka
networks: 
  kafka:
    name: kafka
```