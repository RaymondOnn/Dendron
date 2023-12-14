---
id: mug7d1o2rgsasdg2a59yy0w
title: 12_IntroConstraints
desc: ''
updated: 1699966444972
created: 1699964673391
---
An introduction to rest architectural constraints.

By the end of this lecture, you should be able to answer the question What are rest architectural constraints

and how do I know if my web service is restful or not?

Let's start with a question.

Is your architecture restful?

Some developers think that by using Http and Json, they are creating an application that qualifies

as a restful architecture that may or may not be true.

These applications may not be restful, but they may be rest like or rest ish.

Then the obvious question that comes to mind is what makes an architecture restful?

In order for the application architecture to be qualified as restful, it must follow six design rules,

and these six design rules are referred to as the rest Architecture constraints.

Quick bit of history.

The six architectural constraints for Restfulness were described by Roy Fielding in his dissertation

paper in year 2000.

Let's go over these six rest architectural constraints.

Please note my intent here is just to introduce you to these constraints.

You will learn about the details of each of these constraints in dedicated lectures.

So the first architectural constraint for rest is client server.

This constraint suggests the use of client server design principles for implementing the rest APIs.

Second one is the uniform interface that suggests use of well-defined contracts between the client and

the server.

Third one is the statelessness, which suggests that the server should not manage the state of the application.

Fourth one is caching.

The server should use Http caching headers to cache the responses to the requests received from the

client.

Fifth one is layering.

This suggests that the architecture should be layered and each of these layers should be manageable

independently.

The last one is code on demand, and this suggests that server can not only send data to the client

in response to the requests, but it also can send code that the client can execute.

This constraint is optional.

So what that means is that as long as your implementation is following the first five constraints,

your architecture will be considered as restful.

Recall that rest APIs are not restricted to Http protocol.

So if your API is using Http and it is following the rest architectural constraints, then it is said

to be an Http rest API or rest over http.

Next, I'll introduce you to the Richardson maturity model.

Richardson Maturity model provides a way to measure the restfulness of an API architecture or API implementation.

The idea here is that the architecture for API is evaluated for maturity and it is assigned a score

and the score is between 0 and 3.

An API that is fully compliant with all of rest constraints is considered to be at level three, whereas

an API that does not follow the rest constraints is considered to be at level zero.

Leonard Richardson created this model by analyzing hundreds of web services designs.

My intent here was to just introduce you to this model.

I will be covering the details of Richardson maturity model in a dedicated lecture.

Let's go over what we have covered so far.

Web services used to implement RPC over Http, but those services were rest like or rest ish, but they

were not restful.

To be qualified as a restful service, the architecture must follow the six rest constraints, the intersection

of the six rest constraints and the Http protocol leads to the creation of rest over Http APIs or restful

Http APIs.

Richardson Maturity model is used to measure the maturity of the restful architecture.

The model assigns the implementation a score between 0 and 3.