---
id: qkmtyyaxuzbk933gbvyuhxn
title: 35_APICaching1
desc: ''
updated: 1699967097257
created: 1699965387304
---
Rest API.

Caching part one of two.

In my discussion on rest API constraints, I discussed the caching for rest APIs at a very high level

in this lecture and the next lecture we'll go deeper into how caching applies to rest APIs.

At the end of this lecture you should be able to describe the general concepts of caching and also the

benefits of caching.

Also, you will know all the design decisions you will have to make for cache implementation for your

API.

Let's look at the typical path length traversed by an API invocation.

The API consumer may be a browser based application or a mobile application.

When a user takes an action on the application, the API call is initiated.

The first touch point outside the application is the Internet service providers infrastructure.

From there, the call goes to the edge of the enterprise.

The red line here represents the firewall of the enterprise.

The idea here is that the only entry point is through some kind of a gateway device, which is typically

a hardware example is Datapower example is management software, which we will cover in later sections.

The call is authenticated and some other rules are applied on the gateway.

And if all the rules are satisfied, the call goes inside the enterprise's network.

There may be other functions that may be performed by the gateway.

The example is SSL offloading, or there may be some kind of transformation that may occur on the data.

Now, those aspects, I'll just leave it on the side for the time being and move to the next step.

So the gateway, if everything is good, forwards, the call to the internal application server that

is hosting the API.

It may also be a node application and then the code invokes the backend databases.

If you look at this path length, the caching can occur on any of these touch points.

The client browser may be caching the internet service providers, proxies may be caching.

Gateway has the opportunity to cache the mid-tier servers.

The web application server or the node application can cache, and the modern database is the NoSQL

databases are also caching these days.

So as you can see, caching can occur on any of the touch points in the path length.

So what are the benefits of using cache?

The most cited reason for the use of cache is it improves performance.

No doubt it does improve performance, but there is a bigger reason why you should consider caching.

And the reason is that caching improves the scalability and throughput of your application of your API.

Let's consider a scenario.

In this scenario, you design the application and the APIs keeping in mind that you can You will get

30 calls per second.

Things worked out fine, but after a while your application became really popular, so the API call

volume started to go up and at some point your users started to complain that their API calls are failing.

After the investigation, you realize that although the application was built for 30 calls per second,

your call volume has gone up to 60 calls per second.

Further investigation reveals that the bottleneck is on the database end.

Your database cannot handle more than 50 calls per second.

To resolve this situation, you start to look at the options.

One common option that people think about is let me scale the mid tier vertically or horizontally.

That will not obviously help in this situation.

So what you do is you use caching, you can cache in the mid tier.

Now what will happen is when the call will come from the consumer to the mid tier, it will not go to

the database for a number of these calls and the response will be sent directly from the mid tier to

the consumer or the application.

The idea here is that you have protected your back end from these calls.

In fact, you don't have to do caching on the mid tier as suggested earlier.

Caching can happen on any touch point, so you can actually cache on the gateway, you can cache on

the client.

The idea here is that the closer the caching is done to the consumer, the better will be the performance

and higher will be the the scalability and the throughput.

When deciding on what to cache and how long to cache, you need to keep three factors in mind.

The first factor is the speed of data change.

Second is time sensitivity of the data that you are caching.

And third is security of the data static content such as HTML, jpeg.

PDFs.

They don't change as frequently and they don't contain any time sensitive data.

And security is not an issue for them, so they can be cached for longer period of times.

Days, weeks.

Even months.

With API you are dealing with dynamic data and not static data.

And for dynamic data, these three factors are very critical.

Speed of change, time, sensitivity and security.

Let's take a look at a couple of examples.

For shock marker.

The speed of change is very rapid.

The data is changing or stock tickers are changing every second.

In order to make trade decisions, you need to have the latest and the greatest data from the stock

market.

So it's time sensitive.

If you are carrying out trades using the data you are receiving, then security is also important.

You don't want others to know how you are trading, what you are trading in news articles.

News articles are published on a minutes basis.

It's not time sensitive in the sense that, you know, even if you don't read the news for ten minutes,

there's not going to be any financial loss.

Security perspective.

There is no security issue because all these news articles are in the public domain.

Customer profile.

Every enterprise maintains customer data.

If the customer data is changing and it's not reflected in the call center, it may not be a big deal.

But for some enterprises it may be a big deal.

So it may be time sensitive.

Speed of change perspective.

The customer data may not be changing on a daily basis.

Security.

Security is very high for the customer profile, so you don't want to cache any customer data in the

intermediaries.

Acme's vacation packages.

Now, this one is some data which is mostly public in nature.

Speed of change, Maybe 24 hours.

The data changes time sensitivity.

There may be some time sensitivity factor for some of the vacation packages which may offer discount

for a limited period of time, for example, a few hours.

So you don't want to cache any such data, which is time sensitive, which will expire in terms of offer.

For example, security does not have a issue from the vacation packages perspective.

The data is in the public domain.

So this was just to give you an idea what you have to think through from the data perspective when you

are designing your caching implementation.

As the designer of an API, you will be responsible for making decisions around the caching behavior

of your API.

The first question is which component should control the caching?

The answer to this is very simple.

Your API will control the caching behavior.

Next thing you need to think about is what to cache and who can cache from the what to cache perspective.

I discussed the time sensitivity, the speed of change of data security requirement for data that is

what governance, what to cache, who can cache.

Again, your API is in control and can decide whether the intermediaries can cache the data or not.

Whether the application can cache the data or not.

Next thing is how long is the data cache for or what is the lifetime of the cache?

That aspect, again, depends on the nature of the data, the speed of change, the time sensitivity.

The good news is that there is an easy way of managing cache behavior and that is by way of the Http

cache control directive.

We will discuss the Http cache control directive in details.

Let's summarize In this lecture.

I covered the general concepts of caching, the benefits of caching.

It enhances performance.

It leads to higher scalability of your API, your applications.

What data do cash is?

One of the questions that we discussed.

How do you decide whether the data is good for cashing or not?

Speed of change of data.

Time sensitivity of data and security requirements for your data would decide whether it's a good candidate

for caching or not.

There are certain design decisions that you have to make as a developer of the API.

First one is you have to decide who can cache the data.

Also for how long the cache data is valid.