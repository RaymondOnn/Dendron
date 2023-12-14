---
id: fbb4yk8a692of7he3sq7929
title: 58_APIAnalytics
desc: ''
updated: 1699967822790
created: 1699966000719
---
API Management Analytics.

In this lecture I'll cover the topic of analytics from the API management perspective.

Why do you need API analytics?

And I'll also cover what type of API analytics is supported on the API management platforms today.

At the end of the lecture I will give you a quick demo of Apigee and MuleSoft support for analytics.

Before I get into the details of analytics, let's go over why API analytics is needed.

The very first reason is service improvement.

As you gather analytics, you will understand your APIs better.

You'll see the usage of your APIs and you will be able to make it aligned with the needs of your consumers.

Second is to catch errors.

So sometime there may be errors happening in the API with nobody reporting to you.

Especially if your APIs are exposed to public domain developers.

They may be ignoring your errors, but you cannot ignore those errors.

So you should be looking into the analytics to see what's the error rate and what is the nature of those

errors.

Understand threats and attacks.

Attacks are something that you cannot stop from happening.

But if you understand the kind of threats and attacks which may be going on against your APIs, then

you will be in a better position to protect yourself.

So analytics will help you understand those threats and attacks that may be ongoing on your APIs.

The last one is the business support.

Now, the reason I have put business support at the end is because this is not a common type of analytics

in the context of API, but I'm going to cover it because I think it is an important one.

The business analytics can be captured at the API proxy level by introspecting the data that is going

through the API proxy.

This can give some interesting insight to the business on how some of these transactions are being invoked

by the third party application or even by the internal applications.

I like to think about analytics in two perspective.

First one is the metrics, the typical error rate, the response time throughput, etcetera.

And the other one is visibility, visibility into the usage of the API and visibility into the transactions.

Let's go through the details of each of these now.

There are multiple types of metrics that you would be gathering for your APIs.

First one is the performance metrics.

Examples.

Here are the average response time, the average throughput and the peaks and valleys for your APIs.

Next one is the error rates.

You would be interested in knowing the kind of errors that are happening on your API at various touch

points.

For example, you would like to know the API errors.

In other words, if the API implementation itself is throwing any errors.

You would also like to know if the back end has any errors and what's the back end error rate.

The next one is the policy errors.

How many API calls are being rejected because there is a violation of the quota or a rate limit or a

spike arrest.

That is something which will give you an idea if you need to update your policy.

So for.

For example, today if you are giving five calls per second rate limit to your public developers and

you see too many errors, maybe you want to change it to 10 or 20 depending on how these errors are

happening and how many times are they happening per second.

Next one is the metrics that would help you understand if you are able to provide the agreed upon and

if the consumers are also sticking to their end of the SLA.

Analytics from visibility perspective.

The first type is the usage analytics.

Under this, you may be interested in finding out who is the most active app developer who has subscribed

to your APIs.

You may want to carry out some usage analytics on your APIs.

Which of your APIs are most popular?

You may want to carry out the region based analytics under which you may want to understand from which

region are your APIs getting invoked?

On the fourth one under This is the device type analytics where the idea is to understand from which

devices are your APIs getting invoked.

Next is a transactional analytics.

Now, this is a very business specific aspect.

Most management platforms do not support this aspect.

There are very few that support it.

So you have to look at your specific needs and understand if you would like to leverage a management

platform with or without transactional analytics.

The logic for the transactional analytics need to be built in the API proxy.

Example of such analytics is origin of maximum sales.

By looking into the credit card data that is passing through the proxy, all API management platforms

support analytics, but there is a big variation in how they have implemented the features around the

API analytics.

This is the API dashboard for my account.

And what this is showing you is the data that it has gathered as part of the analytics.

And here the red part are the errors.

You can create this graph on the hourly basis, a daily basis.

You can see that the total traffic is 406.

289 calls were successful.

There was an error rate of 28.82%.

I can get the details of these errors by digging deeper into the analytics.

The average transaction per hour is 406, and if you look at the developer engagement, the most active

developer is sending 50 transactions per hour.

And in the last one hour, geolocation tests have been conducted and this is the traffic composition

for that.

So I'm on the MuleSoft Anypoint platform.

Now let me show you the analytics on this platform.

It allows you to create dashboards and create charts on your own.

So I created a quick chart for the travel API and basically you can create multiple types of charts

from the analytics data that MuleSoft is collecting.

So here, as you can see, I have created a chart for the travel API with the response time, the average

response time for my API and it shows some good graphs.

And also, as I was indicating earlier, the analytics capabilities on each of these platforms vary

a lot.

You have to look at each of these platforms to understand better as to what capabilities they provide

and what makes most sense for your APIs.

In this lecture I covered the topic of analytics.

I discussed why analytics is needed.

It is needed for improvement, understanding the errors and threats, and to generate some business

insights to support the business.

Apart from providing the usual metrics such as performance error rates and SLA, you would also get

visibility into the usage and the business transaction by way of analytics.