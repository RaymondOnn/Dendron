---
id: 78q6y43lr6zec9inpt36h8p
title: 36_APICaching2
desc: ''
updated: 1699967116192
created: 1699965407046
---
Rest API.

Caching Part two of two.

In the previous lecture I discussed the benefits of caching as an API developer or designer, you must

consider use of caching for your APIs.

In this lecture I'll cover some of the common cache control directives that may be used from APIs Cache

Control directives.

I've taken the content for this lecture from the Http specifications document RFC 2616.

If you would like to delve deeper into the topics I'm covering in this lecture, I suggest that you

read through the RFC 2616 documentation.

Cache control directives Allow the API designer or developer to control the caching behavior of the

API.

The cache Control Directors.

The API designer can control who can cache how long the data can be cached for and under what conditions

means the data can be cached.

I've taken this statement directly from the documentation.

Cache control directives must be obeyed by all caching mechanisms along the request and response chain.

Let's look at what it means.

This is a typical API call flow.

When the API sends back a response to a request, it also sends back the cache control header with the

cache control directives.

These cache control directives are then read by the intermediate touchpoints such as the Edge Gateway,

such as the ISP infrastructure components such as the browser.

The expectation is that if all these components are following RFC 2616, they must honor or obey the

cache control directive.

Same thing is true for the request.

The request can also have the cache control directives and it can override the caching behavior implemented

by the API developer.

We'll cover that aspect in the later part.

The cache control header can be set with multiple directives and these directives are provided as a

comma separated list.

Example here is private comma max age equal to 60.

Don't worry about the details of this because we will cover it in more depth.

Now, let's quickly go over what you have learned till now.

Can set the cache control header in the response.

It can control who can cache the response for how long and under what conditions.

The consumer can set the cache control header in the request and it can override the caching behavior.

Also, it can protect sensitive data from being cached.

Public versus private cache control directive.

Sensitive data should not be cached on the intermediaries.

Consider a scenario where you are writing an API for a banking application.

The user can use a browser based front end to invoke the API.

That in turn gives the details of the account.

The account details are sensitive.

It's an example of sensitive data or information.

It is meant only for a single user.

It's private data.

Now, in order to control the caching behavior here, that is, you don't want the intermediary to cache

the data.

You can set the cache control with the private detective.

So in this scenario, for example, the data will not be cached on the intermediaries that is in the

Gateway or the ISP's infrastructure, but it will be cached on the browser.

In case you do not specify private in the cache control, then by default it is public no store cache

control directive browsers write data to local file system and the purpose for this is to implement

cache.

Or it may even be for some other functions such as the back key function or for displaying the information

on the pages or for the history.

The data stored on the local file system sometime gets replicated by way of backups and it may end up

in hands of people who should not be having access to it.

Sensitive data should not be stored anywhere.

And again, banking data is an example of sensitive data, and medical records are an example of sensitive

data in order to prevent such storage, the API designer can use the no store cache control directive.

So if this is specified in the cache control header, the behavior will be that the data will not be

stored anywhere on any touchpoint.

No cache cache control directive and the tag http header.

As the name indicates, the no cache cache control directive may be used for preventing any kind of

caching to be happening on any touch point in the path length.

In other words, the.

If you're a consumer, always get the data from the server by invoking the API.

You may want to do this because the same URL may respond back with different response data.

As a result, you don't want any kind of caching, but this can obviously lead to some inefficiencies

and you are losing the benefit of caching.

And for that to prevent that loss of benefit, you can use Etag header.

Now, the E-deck header can be used to check if the data has changed.

Let me explain it to you.

The consumer sends a request to the API and the API response back with some response data along with

the response data.

The API also sends back what is known as the Etag header.

The Etag header has a value set to some kind of a hash of the response data.

So if the response changes, the hash also changes in the subsequent request.

The consumer sends the request along with the Etag header that it received in the original response.

The API implementation looks at the etag value and compares it with the etag value for the new response

data.

If the tag value is still the same, in other words, the response data has not changed on the server

end, then it sends back a response header with the code 304 not modified.

If the consumer receives this 304 response, it simply uses the data or the response it is holding.

If the data has changed, the server will send back a response code of 200 with the new response data

and a new etag value.

Maxage Cache Control Directive.

The Maxage Cache Control Directive allows the developer to control how long the cache will be valid.

The value is specified in seconds.

Let's take a quick look at some examples now.

So Cache-control max age equal to 60 means that the caching can be done on the intermediary or on the

user's browser, but the maximum validity period for the received data when it is cached is 60s.

Next example is Cache-control private max age equal to 60.

In this case we are instructing the intermediaries not to cache because it's private, so the browser

will be able to cache the data and the maximum period for which the cache data will be valid is 60s.

Next example is cache control.

No cache.

In this case, the touch points are instructed not to cache, but the browser can validate by sending

the etag value.

Now let's go over some of the best practices from caching perspective.

As an API designer or developer.

You must check if it is possible to take advantage or of caching in your API, especially if you are

dealing with a high volume API or large data sets for responses.

Always take care of your sensitive data.

Use no store and private.

If you are using no cache directive, then consider supporting the Etag header for validation, especially

for large responses so you can prevent network latency for large responses by supporting the Etag head.

Last one.

Maxage is the most commonly used cache control direct, but if the value is not set appropriately for

Maxage, it can lead to some unexpected results and user complaints.

So you must set up the value of maxage depending on the refresh speed of your data.

So you have to come up with that optimal value and test it before releasing your API.

Let's summarize In this lecture.

I covered some of the commonly used cash control directives for APIs.

If you would like to know about the other directives that are not covered in this lecture, I suggest

that you go through the documentation for the Http protocol specifications.

RFC 2616.

Both the provider and the consumer can use the cache control header and the directives.

The server can use the cache control directives to instruct the intermediaries and the browser on how

to cache or how to manage the cache for responses.

The consumer can set the cache control directives in the request header to override the behavior set

for caching by the server.

Public versus private Cache Control Directive.

If you're dealing with sensitive data, then you would not like that sensitive data to be cached on

the intermediaries.

In that scenario, as a designer, you can use the cache control directive private.

If you don't specify the public or private by default, the cache control directive is public.

In other words, all the intermediaries will be instructed to cache the data.

Typical implementation of cache involves writing the data to local storage.

Local storage may get backed up to an external device tape, for example.

In that scenario, unauthorized people may have access to that data.

For sensitive data such as bank account details or medical records, you would not like that to happen.

So you can specify in the cache control directive no scope.

If your data is changing at a very high frequency or if the change rate is unpredictable, then you

can use no cache.

The idea is that with no cache, the consumer will always request the data from the server or the API.

But unfortunately this would lead to some inefficiency and to prevent the inefficiencies, you can use

the Etag header where the consumer can use the Etag to check with the server.

If the data has changed.

If the data has changed, only then it gets all the data.

Otherwise, the consumer can use the data that it is already holding.

Max age is the most commonly used cache control directive that controls the lifetime of the cache.

You have to be very careful when setting the max age as it can lead to unpredictable behavior and user

complaints if not set correctly.