---
id: l0mtzdsj4b9j3ljfo4o0xtd
title: docker
desc: ''
updated: 1700852087196
created: 1694661691700
---

## Basic Setup: Single Kafka Node

-   The Kafka cluster requires Zookeeper and Kafka brokers, which is very useful in such a case.
-   NOTE: Latest versions of Kafka can now run in KRaft mode, without the need for zookeeper
-   To set up a Kafka cluster, we need to run two services: Zookeeper and Kafka Brokers.

```yaml
version: "3"
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

<details> 
  <summary>Multi Node Setup</summary>

```yaml
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

</details>

### [Config: Kafka Listeners](https://www.confluent.io/blog/kafka-listeners-explained/)

> NOTE:
>
> -   You need to set advertised.listeners (or KAFKA_ADVERTISED_LISTENERS if you’re using Docker images) to the external address (host/IP) so that clients can correctly connect to it.
> -   Otherwise, they’ll try to connect to the internal host address—and if that’s not reachable, then problems ensue.

#### The Communication Process

-   Data is read from and written to the leader for a given partition, which could be on any of the brokers in a cluster.
-   When a client (producer/consumer) starts, it will request metadata about which broker is the leader for a partition—and it can do this from any broker.
-   The metadata returned will include the endpoints available for the Leader broker for that partition, and the client will then use those endpoints to connect to the broker to read/write data as required
    -   i.e. The actual host and IP that it will connect to for reading/writing data is based on the data that the broker passes back in that initial connection

#### What are listeners?

-   Listeners are what interfaces Kafka binds to
    -   A listener is a combination of:
        -   Host/IP
        -   Port
        -   Protocol
    -   Kafka brokers can have multiple listeners.
-   There are different types of listeners
    -   When connecting to a broker, the listener that will be returned to the client will be the listener to which you connected (based on the port).
    -   Kafka brokers communicate between themselves, usually on the internal network (e.g., Docker network, AWS VPC, etc.) via the `KAFKA_INTER_BROKER_LISTENER_NAME`(inter.broker.listener.name). The host/IP used must be accessible from the broker machine to others.
    -   ADVERTISED_LISTENERS are how clients can connect.
        -   Each listener will, when connected to, report back the address at which it can be reached.
        -   The address at which you reach a broker depends on the network used.
        -   If you’re connecting to the broker from an internal network, it’s going to be a different host/IP than when connecting externally.

#### Connecting to Kafka in Docker
##### Communication within the Docker network
- This could be inter-broker communication (i.e., between brokers) and between other components running in Docker, such as Kafka Connect or third-party clients or producers.
- For these comms, we need to use the hostname of the Docker container(s). 
- Each Docker container on the same Docker network will use the hostname of the Kafka broker container to reach it.
- See DNS portion of docker [[tools.cicd.containers.docker.basics.network]] to understand why.

##### Non-Docker network traffic
- This could be clients running locally on the Docker host machine, for example. The assumption is that they will connect on localhost to a port exposed from the Docker container.

``` yaml
# Within Docker Netwerk: Use Listener BOB
# In / Out of Docker Network: Use Listener FRED
kafka0:
    image: "confluentinc/cp-enterprise-kafka:5.2.1"
    ports:
      - '9092:9092'
      - '29094:29094'
    depends_on:
      - zookeeper
    environment:
      KAFKA_BROKER_ID: 0
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_LISTENERS: LISTENER_BOB://kafka0:29092,LISTENER_FRED://kafka0:9092,LISTENER_ALICE://kafka0:29094
      KAFKA_ADVERTISED_LISTENERS: LISTENER_BOB://kafka0:29092,LISTENER_FRED://localhost:9092,LISTENER_ALICE://never-gonna-give-you-up:29094
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: LISTENER_BOB:PLAINTEXT,LISTENER_FRED:PLAINTEXT,LISTENER_ALICE:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: LISTENER_BOB
```