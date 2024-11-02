---
id: c7k6crziyo84fq88kdouuz1
title: wait
desc: ''
updated: 1728231916416
created: 1723485121268
---

Where the Problem Lies
It has always been the case that Python’s multi-threaded performance has never lived up to expectations because of GIL.

So since version 3.4, Python has introduced the asyncio package to execute IO-bound tasks through concurrency concurrently. After several iterations, the asyncio APIs have worked very well, and the performance of concurrent tasks has improved dramatically compared to the multi-threaded version.

However, there are still many mistakes that programmers make when using asyncio:

One mistake, as shown in the figure below, is to use the await coroutine method directly in a way that changes the call to a concurrent task from asynchronous to synchronous, ultimately losing the concurrency feature.


Another mistake is shown in the figure below, although the programmer realizes that he needs to use create_task to create a task to be executed in the background. However, the following way of waiting for tasks one by one turns the tasks with different timings into an orderly wait.


This code will wait for task_1 to finish first, regardless of whether task_2 finishes first.

What is concurrent task execution?
So, what is a real concurrent task? Let’s use a diagram to illustrate:


No matter how many tasks we spawn, we will eventually need to join back. Image by Author
As the diagram shows, a concurrent process should consist of two parts: starting the background task, rejoining the background task back to the main function, and getting the result.

Most readers will already know how to use create_task to start a background task. Today, I will introduce a few ways to wait for a background task to complete and the best practices for each.

Getting Started
Before we start introducing today’s main character, we need to prepare a sample async method to simulate an IO-bound method call, as well as a custom AsyncException that can be used to kindly prompt an exception message when the test throws an exception:


Comparison of methods for concurrent execution
Once we have done the preparations, it’s time to start the day’s journey and fasten your seat belt.

1. asyncio.gather
asyncio.gather can be used to start a set of background tasks, wait for them to finish executing, and get a list of results:


asyncio.gather, although it forms a group of background tasks, cannot accept a list or collection as an argument directly. If you need to pass in a list containing background tasks, please unpack it.

asyncio.gather takes a return_exceptions argument. When the value of return_exception is False, if any background task throws an exception, it will be thrown to the caller of the gather method. And the result list of the gather method is empty.



Screenshot. Image by Author
When the value of return_exception is True, exceptions thrown by background tasks will not affect the execution of other tasks and will eventually be merged into the result list and returned together.

results = await asyncio.gather(*aws, return_exceptions=True)

Screenshot. Image by Author
Next, let’s see why the gather method can’t accept a list directly, but has to unpack the list. Because when a list is filled and executed, it is difficult to add new tasks to the list while we wait for them to finish. However, the gather method can use nested groups to mix existing tasks with new ones, which solves the problem of not being able to add new tasks in the middle:


However, gather cannot set the timeout parameter directly. If you need to set a timeout for all running tasks, use this pose, which is not elegant enough.


2. asyncio.as_completed
Sometimes, we must start the following action immediately after completing one background task. For example, when we crawl some data and immediately call the machine learning model for computation, the gather method cannot meet our needs, but we can use the as_completed method.

Before using asyncio.as_completed method, let’s look at this method’s source code.

# This is *not* a @coroutine!  It is just an iterator (yielding Futures).
def as_completed(fs, *, timeout=None):
  # ...
  for f in todo:
      f.add_done_callback(_on_completion)
  if todo and timeout is not None:
      timeout_handle = loop.call_later(timeout, _on_timeout)
  for _ in range(len(todo)):
      yield _wait_for_one()
The source code shows that as_completed is not a concurrent method, and returns an iterator with a yield statement. So we can directly iterate over each completed background task, and we can handle exceptions for each task individually without affecting the execution of other tasks:


as_completed accepts the timeout argument, and the currently iterated task after the timeout occurs will throw asyncio.TimeoutError:



Screenshot. Image by Author
as_completedis much more flexible than gather in terms of handling the results of task execution, but it is difficult to add new tasks to the original task list while waiting.

3. asyncio.wait
asyncio.wait is called in the same way as as_completed, but returns a tuple with two sets: done and pending. done holds the tasks that have finished executed, and pending holds the still-running tasks.

asyncio.wait accepts a return_when parameter, which can take three enumerated values:

When return_when is asyncio.ALL_COMPLETED, done stores all completed tasks, and pending is empty.
When return_when is asyncio.FIRST_COMPLETED, done holds all completed tasks, and pending holds the still-running tasks.


Screenshot. Image by Author
When return_when is asyncio.FIRST_EXCEPTION, done stores the tasks that have thrown exceptions and completed execution, and pending holds the still-running tasks.
When return_when is asyncio.FIRST_COMPLETED or asyncio.FIRST_EXECEPTION, we can call asyncio.wait recursively, so that we can add new tasks and keep waiting for all tasks to finish, depending on the situation.