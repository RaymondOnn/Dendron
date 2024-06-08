---
id: ace2sfzkmwusfif2w3oied6
title: Deploy
desc: ''
updated: 1715529229063
created: 1715529045856
---

https://www.reddit.com/r/ExperiencedDevs/comments/12c3f52/do_you_have_a_guideline_on_logging/
https://www.reddit.com/r/ExperiencedDevs/comments/143tray/what_are_some_standards_when_it_comes_to_logging/


Use a logger that has configurable levels, i.e. trace, debug, info, warn, error, fatal that are easy to set at runtime, e.g with an environment variable.

Use a logger that can be configured with structured output so it can be aggregated, searched, and alerted upon.

Consider also instrumenting the application with metrics and tracing (e.g Prometheus endpoint, OpenTracing) so you can have a more targeted way of monitoring and investigating production problems beyond log messages.

Some practical advice on logging to different levels:

TRACE: This is where you're going to put out logs of every minute step that a function is carrying out, as well as dumping out the full contents of data structures, request and response payloads, and so on. If I'm switching on TRACE logging I expect to see a noisy log of everything that the application is doing. Quite often I'm going to use trace level information to try and investigate problems with the logic and behavior of the application, rather than errors or crashes.

DEBUG: Here I want messages that are useful for trying to figure out why the application is not working but I haven't yet reached for a debugger. So I'm looking for messages that tell me some explicit details about the application state, but not a dump of the entire universe. If my application is crashing, the debug log is going to tell me roughly where in the logic of my application the problem is. Think things like the configuration that has been loaded, the data that is being processed, and metadata about remote calls being made but not the full trace.

INFO: Info should just be for reporting on the overall state of the application's execution in some sort of meaningful summary. When I look at the info log I want to see in very broad strokes what the application is doing, e.g. it's booting up, it's waiting to join the cluster, it's listening, it's waiting for a job to appear on the queue, it's picked up the job and is running, etc.

WARN: Something unexpected happened but it's not an error and the application can tolerate it. Things like a network call failed but there's retry logic so it will try again, or some payload was missing a field but we can still process it. Warnings are also useful for letting operators know their configuration will work but should not be used in production, e.g. warning about expired certificates, about replication or caching not being configured and so on.

ERROR: Something bad happened but the application doesn't need to exit. Use this for situations like API calls that have failed or timed out after being retried, or data that could not be processed. This is likely something you're going to want to monitor and alert on, e.g. if my authentication service suddenly starts spewing error log messages I want to know about it.

FATAL: Something has gone terribly wrong application has to exit. Tell me why. Give me a stack trace. Give me as much actionable information as possible to give me some hints about what to do next and where to look for the root cause of the problem. For example, if my application needs to be configured with access to a SQL database but I haven't specified this, give me a log that tells me what the application needs don't just say "failed to connect to database." If a job failed to complete because a remote call failed with 401 unauthorized tell me so, don't just say "job failed" and exit the worker process.

Great post!!!

I’d additionally add that, if you’re working at scale or in a distributed system, metrics are helpful for getting the “lay of the land” / figuring out which logs to look at during an investigation. Depending on your logging setup, you may need to look in multiple different locations for different service logs, and grepping across multiple files for error messages can get old fast.

Generating metrics is also typically faster than logging errors, so you may also be able to catch some errors using metrics before the related logs even get indexed. Very helpful for e.g. periodic service health checks! Additionally, logging systems can get pricey depending on how your logs are stored / queried and metrics are designed to be extremely cheap to store and query.

But, probably the biggest benefit of using metrics is that it’s difficult to build a good service monitoring dashboard using only logs, and your engineering team will look 150% as credible if you have a monitor with a bunch of squiggly lines behind your desks.

Two main ideas here: “text” logs versus spans/traces.

Generally, for text logs only log errors (yes this includes json formatted logs). Seeing logs should be a surprise and indicative of something that should be noticed/escalated.

For other observability, use spans/traces. This gives you all the instrumentation you need to get into the details of what your app is doing, and where. Because these are used heavily, you probably won’t keep the span info around for more than a couple weeks, unless it is related to an error. This replaces all the “got here” and “it works” things you would otherwise be logging.

Using spans also lets you generate time series metrics without involving your service code. This is super helpful for making dashboards and retaining select data more than it would otherwise be kept around.

Finally, traces can be put together easily so you can see all related events across multiple separate microservices. And since they have timestamps you can correlate it with your error logs.

Check out opentelemetry, a lot of the work may already be done for your stack. Of course, for a prototype, disregard all this and use stdout :)


Tacking onto myself here, for text logs, generally try to go with an format like json, and contextualize them as much as you can. (App version, timestamp, currently authenticated user id, stack trace)