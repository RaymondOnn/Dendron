---
id: b9ji63cpkm2hbpls5qam8dv
title: Windowing
desc: ''
updated: 1694629853737
created: 1694628739878
---

https://medium.com/@ofili/windowing-in-stream-processing-8bb636bdfaa7

## What is windowing

- Windowing is a way of grouping events into a set of time-based collections or windows.
- These windows can be specified based on time intervals, number of records, or some other criteria.
- The aim of windowing is to allow stream processing applications to break down continuous data streams into manageable chunks for processing and analysis.
- Windowing is often used in stream processing applications to implement operations like aggregations, filtering, and transformation.
- By breaking down the stream into manageable chunks, it becomes easier to carry out operations that require knowledge of the state of the stream over time.

## Windowing in PyFlink

In PyFlink, windowing can be done on keyed streams or non-keyed streams.

### Keyed streams

- Keyed streams are streams that have been partitioned based on some key attribute.
- In PyFlink, keyed streams can be windowed using time-based windows, count-based windows, or session windows.

#### Time-based windows

- Time-based windows are windows that are based on a specific time interval. 
- In PyFlink, time-based windows can be defined using a sliding window or a tumbling window.
- A tumbling window is a window of fixed size that moves along with the stream. 
  - For instance, you could define a tumbling window of 10 seconds on a stream. This means that PyFlink will create a new window every 10 seconds and aggregate the data within each window.
- A sliding window, on the other hand, is a window that slides over the stream. 
  - This means that PyFlink will create a new window after every specified interval, and the window will overlap with the previous window. 
  - For example, if you define a sliding window of 10 seconds with a slide of 5 seconds, PyFlink will create a new window every 5 seconds, and the window will overlap with the previous window by 5 seconds.

```py
from pyflink.datastream import StreamExecutionEnvironment
from pyflink.datastream.window import TimeWindow
from pyflink.datastream.functions import AggregateFunction

class SumAggregator(AggregateFunction):
    
    def create_accumulator(self):
        return 0
    
    def add(self, value, accumulator):
        return value + accumulator
    
    def get_result(self, accumulator):
        return accumulator
    
    def merge(self, a, b):
        return a + b

# Init streaming environment
env = StreamExecutionEnvironment.get_execution_environment()

# Create stream with four values
stream = env.from_collection([(1, 2), (2, 3), (3, 4), (4, 5)])
sums = stream.key_by(lambda x: x[0]) \
             .window(TumblingEventTimeWindows.of(Time.seconds(10))) \
             .aggregate(SumAggregator())

sums.print()
env.execute("Windowed stream")

```

#### Count-based windows

- Count-based windows are windows that are based on the number of records in the stream.
- For instance, you could define a count-based window of 1000 records on a stream. This means that PyFlink will create a new window every 1000 records and aggregate the data within each window.

``` py
from pyflink.datastream import StreamExecutionEnvironment
from pyflink.datastream.window import CountWindow
from pyflink.datastream.functions import AggregateFunction

class SumAggregator(AggregateFunction):
    
    def create_accumulator(self):
        return 0
    
    def add(self, value, accumulator):
        return value + accumulator
    
    def get_result(self, accumulator):
        return accumulator
    
    def merge(self, a, b):
        return a + b

env = StreamExecutionEnvironment.get_execution_environment()
stream = env.from_collection([(1, 2), (2, 3), (3, 4), (4, 5)])
sums = stream.key_by(lambda x: x[0]) \
             .window(CountWindow.of(2)) \   # define count-based window of size 2 
             .aggregate(SumAggregator())

sums.print()
env.execute("Windowed stream")
```

#### Session windows

- Session windows are windows that group events based on gaps in the stream. 
- PyFlink will create a new window when there is no data in the stream for a specified period.
- For instance, you could define a session window of 10 seconds on a stream. This means that PyFlink will create a new window when there is no data in the stream for 10 seconds.

``` py
from pyflink.datastream import StreamExecutionEnvironment
from pyflink.datastream.window import SessionWindow
from pyflink.datastream.functions import AggregateFunction

class SumAggregator(AggregateFunction):
    
    def create_accumulator(self):
        return 0
    
    def add(self, value, accumulator):
        return value + accumulator
    
    def get_result(self, accumulator):
        return accumulator
    
    def merge(self, a, b):
        return a + b

env = StreamExecutionEnvironment.get_execution_environment()
stream = env.from_collection([(1, 2), (2, 3), (3, 4), (6, 5), (10, 2)])
sums = stream.key_by(lambda x: x[0]) \
             .window(SessionWindow.with_gap(Time.seconds(5))) \
             .aggregate(SumAggregator())

sums.print()
env.execute("Windowed stream")
```

In this example, we create a stream with five values, with varying time intervals between them. We key the stream by the first value and define a session window with a gap of 5 seconds using the SessionWindow function. We then aggregate the values in the stream using the SumAggregator function and print the results.

### Non-keyed streams

- Non-keyed streams are streams that have not been partitioned based on any key attribute.
- In PyFlink, non-keyed streams can only be windowed using time-based windows.

#### Global windows

- Global windows are windows that include all the events in the stream.
- PyFlink will create a new window after a specified period, and all the events in the stream will be aggregated within the window.

## Choosing the appropriate window type

- When choosing the appropriate window type, you should consider the nature of your data and the processing requirements.
- Here are some factors to consider:
  - Data arrival rate: If data arrives at a fixed rate, time-based windows might be appropriate. If data arrives in bursts or irregular intervals, session windows might be more appropriate.
  - Data volume: If your data volume is high, count-based windows might be more efficient. Time-based windows can result in large numbers of small windows, which can negatively impact performance.
  - Processing requirements: If you require real-time processing of data, time-based windows with short intervals might be necessary. If you can afford a delay in processing, larger windows might be appropriate.
  - Data relationships: If your data has relationships with other data points, session windows might be necessary to group related data together.
  - Data skew: If your data is skewed, meaning some keys have significantly more data points than others, you might need to adjust your window size or use a different window type to ensure fairness in processing.
