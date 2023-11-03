---
id: 0mz0dynn0suqlhes5o90nsx
title: processing
desc: ''
updated: 1697306943624
created: 1697306837954
---
<https://medium.com/@akash.d.goel/stream-processing-fundamentals-d4090f33451d>


## Stream Processing

- Stream processing is an application design pattern for processing unbounded streams of events.
  
### Maintaining State  

- Stream processing engines usually provide a set of built-in operations to ingest, transform, and output streams. Operations can be either stateless or stateful.

#### Stateless operations

- Stateless operations do not maintain any internal state.
- Stateless operations are easy to parallelize, since events can be processed independently of each other and of their arriving order.
- in the case of a failure, a stateless operator can be simply restarted and continue processing from where it left off.

#### Stateful operations

- stateful operators may maintain information about the events they have received before.
- This state can be updated by incoming events and can be used in the processing logic of future events.
- Stateful stream processing applications are more challenging to parallelize and operate in a fault-tolerant manner because state needs to be efficiently partitioned and reliably recovered in the case of failures.

### Data Parallelism and Task Parallelism

#### Data Parallelism

- Data Parallelism refers to the approach of partitioning the input data and have tasks of the **same operation** execute on the data subsets in parallel.
- It allows for processing large volumes of data and spreading the computation load across several computing nodes.

#### Task Parallelism

- Tasks from **different operators** performing computations on the same or different data in parallel.
- Using task parallelism, you can better utilize the computing resources of a cluster.

### Data Exchange Strategies

Data exchange strategies define how data is passed between different tasks. There are 4 types of strategies

- Forward strategy: Sends data from a task to a receiving task. If both tasks are located on the same physical machine ,this exchange strategy avoids network communication.
- The broadcast strategy sends every data item to all parallel tasks of an operator. Because this strategy replicates data and involves network communication, it is fairly expensive.
- The key-based strategy partitions data by a key attribute and guarantees that data items having the same key will be processed by the same task.
- The random strategy uniformly distributes data items to operator tasks in order to evenly distribute the load across computing tasks.

### Latency and Throughput

#### Latency

- Latency indicates how long it takes for an event to be processed.
- Essentially, it is the time interval between receiving an event and seeing the effect of processing this event in the output.
- For example, an average latency value of 10 ms means that events are processed within 10 ms on average.
- Alternately, a 95th-percentile latency value of 10 ms means that 95% of events are processed within 10 ms.

#### Throughput

- Throughput is a measure of the system’s processing capacity — its rate of processing.
- Throughput is measured in events or operations per time unit.
- It is important to note that the rate of processing depends on the rate of arrival; low throughput does not necessarily indicate bad performance

Low latency and high throughput are the key characteristics of stream processing and it enables what we call real-time applications. Lowering latency increases throughput and latency can be lowered by increasing parallelism or CPU cores.

### Data Ingestion and Egress

- Data ingestion is the operation of fetching raw data from external sources and converting it into a format suitable for processing.
- Data egress is the operation of producing output in a form suitable for consumption by external systems.

### Operations

On a high level, stream processing engines support following operations :

#### Transformation

- Transformation operations are single-pass operations that process each event independently.
- These operations consume one event after the other and apply some transformation to the event data, producing a new output stream.

#### Rolling Aggregations

A rolling aggregation is an aggregation, such as sum, minimum, and maximum, that is continuously updated for each input event.
Aggregation operations are stateful and combine the current state with the incoming event to produce an updated aggregate value

#### Window Operations

- Window operations continuously create finite sets of events called buckets from an unbounded event stream and let us perform computations on these finite sets.
- Events are usually assigned to buckets based on data properties or based on time.
- Example -> you might want to know a metric for every 5 minutes.
- There are two types of common window types:
  - Tumbling windows assign events into nonoverlapping buckets of fixed size based on time or count of elements.
  - Sliding windows assign events into overlapping buckets of fixed size. Thus, an event might belong to multiple buckets. We define sliding windows by providing their length and their slide.

### Time Semantics

The streaming application could operate with two different notions of time: processing time or event time.

#### Processing time

- Processing time is the time of the local clock on the machine where the operator processing the stream is being executed.
- A processing-time window includes all events that happen to have arrived at the window operator within a time period, as measured by the wall clock of its machine.

#### Event time

- Event time is the time when an event in the stream actually happened.
- Event time is based on a timestamp that is attached to the events of the stream.
- It completely decouples the processing speed from the results.
- Operations based on event time are predictable and their results are deterministic.
  - An event time window computation will yield the same result no matter how fast the stream is processed or when the events arrive at the operator.

#### Processing Time vs Event Time

- Processing-time windows introduce the lowest latency possible.
  - Since you do not take into consideration late events and out-of-order events, a window simply needs to buffer up events and immediately trigger computation once the specified time length is reached.
  - Thus, for applications where speed is more important than accuracy, processing time comes in handy.
- On the other hand, event time guarantees deterministic results and allows you to deal with events that are late or even out of order.

### Watermarks

- A watermark is a global progress metric that indicates the point in time when we are confident that no more delayed events will arrive.
- In essence, watermarks provide a logical clock that informs the system about the current event time.
- When an operator receives a watermark with time T, it can assume that no further events with timestamp less than T will be received.
- However it is not easy to find a perfect value of watermarks:

#### Optimizing watermarks
- Eager watermarks ensure low latency but provide lower confidence. 
- In this case, late events might arrive after the watermark, and we should provide some code to handle them.
- On the other hand, if watermarks are too relaxed, you have high confidence but you might unnecessarily increase processing latency.

### State and Consistency
Supporting stateful operators comes with a few implementation challenges:

State management: The system needs to efficiently manage the state and make sure it is protected from concurrent updates.
State partitioning: Parallelization gets complicated, since results depend on both the state and incoming events. Fortunately, in many cases, you can partition the state by a key and manage the state of each partition independently.
State recovery: The third and biggest challenge that comes with stateful operators is ensuring that state can be recovered and results will be correct even in the presence of failures.
Task Failure and Result Guarantees
Reprocessing all input to reproduce lost state in the case of failures would be both very expensive and time-consuming. Note that guaranteeing the consistency of an application’s state is not the same a guaranteeing consistency of its output. Once data has been emitted to a sink, it is difficult to guarantee result correctness, unless the sink system supports transactions.

AT-MOST-ONCE: The simplest thing to do when a task fails is to do nothing to recover lost state and replay lost events. This type of guarantee is also known as “no guarantee” since even a system that drops every event can provide this guarantee.
AT-LEAST-ONCE: In most real-world applications, the expectation is that events should not get lost. This type of guarantee is called at-least-once, and it means that all events will be processed, and there is a chance that some of them are processed more than once. In order to ensure at-least-once result correctness, you need to have a way to replay events — either from the source or from some buffer. Persistent event logs write all events to durable storage, so that they can be replayed if a task fails.
EXACTLY-ONCE: Exactly-once is the strictest guarantee and hard to achieve. Exactly-once means that not only will there be no event loss, but also updates on the internal state will be applied exactly once for each event. Providing exactly-once guarantees requires at-least-once guarantees, and thus a data replay mechanism is again necessary. Additionally, the stream processor needs to ensure internal state consistency. That is, after recovery, it should know whether an event update has already been reflected on the state or not.
