---
id: toylqw7uu7200zy7exgcmyj
title: 57_TrafficManagemen
desc: ''
updated: 1699967800997
created: 1699965970329
---

Air traffic management.

In this lecture, I'll discuss the need for traffic management.

At the end of this lecture, you should be able to explain why you need traffic management strategy

for your API and your API infrastructure.

Also, you'll learn about the three common types of traffic management policies that are offered by

most platforms.

I'll also cover some demos in this lecture that would use Apigee API management platform to show how

these common traffic management policies work.

The API provider is in the middle of the API consumer and the data owner.

On one side, the API provider has to make sure that they are fulfilling the contractual aspects that

they have agreed upon with the consumer.

On the other side, it is the responsibility of the provider to protect the data.

There are three main reasons why the API provider should manage the traffic.

First one is the response time consistency for the consumers management of the SLA, and the last one

is protecting the back end.

I'll go through each one of these in details.

Response time consistency.

Let's consider a scenario here.

Let's say there is a database and the database owner conducted some performance tests on the database,

and those tests revealed that up to 100 calls per second.

The query responds back within one second.

And the moment the number of calls go beyond 105 per second, the response time for the query jumps

to five second.

Let's say you have written a API that is using the data in this database and an application developer

is using your API with 30 calls per second.

As you can see, 30 calls per second can be handled without any problem.

The popularity of your API grew and another developer started to use your API.

Let's assume they're also making 30 calls per second and everything is good because 60 calls per second.

It is well below 100.

There is no problem.

At some point developer one decides to update the application and unfortunately they introduced a bug

related to an infinite loop.

After the release of the application one, the call volume suddenly spiked up to 80 calls per second

from application one because of this infinite loop.

Now at this point, the total calls coming to the API is 110, which is above 105 calls.

As a result, the response time for application two will go up from one second to five seconds.

Obviously the owner of Application two will not be happy.

They will suffer because their end users will complain.

They'll get a bad reputation and they will give a bad reputation to the API because they're not getting

a consistent response from the API.

As an API provider, you need to prevent such scenarios.

You want to make sure that applications are not suffering due to that one misbehaving application and

create that consistency in the response times of your APIs.

Traffic Management Service Level Agreement.

When an API consumer signs up to use an API, they are getting into a contractual agreement with the

API provider.

There is typically an associated with the contract which looks like this.

Here.

The seller says that the consumer can make a maximum of 10,000 calls per day and they can be five concurrent

calls per second from the consumer end.

Also, the API provider is guaranteeing that the average response time for the calls will be under one

second.

This is all well and good, but the challenge is how do we enforce it?

For example, is the API giving a response time of under one second?

How do we ensure that that is happening?

That's one aspect that the provider has to take care of.

The other aspect is how do we know that the consumer is not making more than 10,000 calls per day or

making more than five concurrent calls per second?

That's where the enforcement of the SLA on the consumer comes into picture.

So traffic management is necessary from the perspective as well.

Traffic management protecting the backend.

Let's consider a scenario in which you have an internal network that is shared by multiple components,

such as the web application servers or database servers and a server hosting the API.

Let's say the API is in use by multiple applications outside the trusted network.

If there is a sudden spike in volume, maybe because of a denial of service attack or maybe a misbehaving

application and this spike in volume is not controlled on the edge of the enterprise.

In that case, that call volume will start to impact your internal components.

For example, the database may degrade in performance or may even crash.

Your internal network may have too many requests, and that will swamp the network.

And at that point, your other servers, such as the Web application server, will also start to get

impacted.

The API provider must put in some kind of a traffic management control to prevent the backends from

getting impacted in case of such high volume events, whether they are due to a malicious act or not.

This can be done by way of blocking or throttling the requests on the edge of the enterprise network.

Most management platforms provide some mechanism to apply traffic management policies to the proxy for

your APIs.

There are three typical types of policies available for traffic management.

First one is the quota policy.

Second is rate limiting policy.

And the third one is the spike arrest policy.

Now, I'll go through the details of each of these policies.

Quota policy.

The quota policy enables the developer to define a maximum number of calls per unit time that an application

can make to the API.

An API is identified by the API key.

Almost all providers today restrict the number of calls that a public domain application can make to

their APIs.

In other words, they define a quota for the public domain developer apps.

Let's consider the case of Twitter.

Twitter receives 350,000 tweets per minute, and most of these tweets are generated by the API calls

made by their internal applications.

So in other words, they have not restricted the incoming calls from their own applications or it is

a very high number in in the order of millions of calls per hour.

That's the quota for their internal application.

Whereas for the public domain developers who are using the Twitter API, there is a restriction of 350

calls per hour.

Here is another example.

Walmart open API.

This is my account page and as you can see, the quota for me is 5000 calls per day.

If I go beyond 5000 calls per day, my calls will start to fail.

For this demo on the quota policy, I'll use the API API management platform.

I will add a quota policy to the geolocation API with the restriction of six calls per minute and then

we'll test it in the node code which is available on GitHub.

I'm on the API management portal.

I have created a new API called Geolocation Quota that's using the Google geolocation and I'm simply

going to add the quota policy and set it up to six calls per minute.

So as you can see, it is currently set up with 2000 calls per month.

And so I've set it to six and I'll change this to minute and just save it.

And we are ready to test it.

So I've already set up the URL for the API.

As you can see, all the calls went through.

Let's try it again.

And this time all the calls failed because within the one minute period we have already run out of our

quota of six calls in a minute.

Rate limiting.

The rate limiting policy allows the API provider to limit the number of concurrent connections to the

API.

Let's consider a scenario.

Let's say you have an API that can support 100 concurrent calls per second and let's say you have three

consumers or applications.

Now, at any point in time, the number of concurrent connections from these three applications should

not exceed 100.

So the provider can apply the rate limiting policy to restrict the number of concurrent connections

or calls to 20 calls per second from each of these applications.

So the maximum that it can reach is 60 concurrent calls per second, which is less than the 100 concurrent

calls that are supported by the API.

Now we'll try the behavior of the Walmart API from the rate limiting perspective.

The code that I'm going to use is available on GitHub.

For the Walmart Open API.

The number of concurrent calls that I can make is five calls per second.

In this simple node code, I've set up my API key and I've set up the Walmart URL.

I'm making six call in a quick succession so that I can exceed the five call per second restriction

and see if the calls fail.

So let's try it out.

So as you can see, one call out of the six call failed.

Let's try it one more time.

Again, one call out of six times.

So what's happening here is that Walmart API is allowing us to call the API only five times in one second.

After the one second period has passed, I get an additional five calls that I can make in the next

one.

Second spike arrest policy.

The spike arrest policy protects the API backend from severe traffic spikes and denial of service attacks.

It prevents calls beyond certain high watermark from reaching the backend.

Let's say you have a graph that looks like this for your call volume.

On the y axis, we have calls per second and on the x axis we have time in seconds.

If the spike arrest policy is applied, there is some high watermark value defined and the spike arrest

policy at runtime will make sure that the call volume or the calls per second never exceeds the high

watermark.

In this demo, I'll add the Spikers policy to this geolocation API and test it with the node code.

The high water mark will be three calls per.

So I have the Spikers policy, which is set to 300 per second.

Stands for per second.

First, we'll test it out with the 300 per second watermark.

Node spike test.

And as you can see, all calls went through.

Now we're going to go ahead and make a change.

I'll change it to three per second.

And run the test again.

And in this case, as you can see, some of the calls failed.

In this lecture I covered the topic of traffic management.

Traffic management is a must.

The provider has to protect the back end.

They have to manage an SLA and they have to provide a consistent response time for their APIs.

And traffic management helps.

There are three typical policies available on the API management platforms for traffic management,

and these policies are the quota policy, the rate limiting policy and the spike policy.

Also in this lecture I demonstrated to you how you can use API management platforms to apply these policies

to your APIs.