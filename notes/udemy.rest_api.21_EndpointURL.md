---
id: fen2yi3afeaio4lrizpoxwf
title: 21_EndpointURL
desc: ''
updated: 1699966669353
created: 1699964894761
---
Rest API endpoint.

By the end of this lecture, you should be able to explain the various parts of the endpoint URL for

an API and you will be familiar with the common terms base URL, route, URL and product.

An API client needs the endpoint URL for the API.

This is a common scheme followed by the APIs for the endpoints.

The first part is the base URL, which has the domain on which the API is hosted.

Second part is a logical group name, which is optional.

Third part is the version of the API.

Then we have the rest resource.

This is the rest resource which will be operated on by way of this endpoint.

And then there is the identity of the resource on which the action is taken by the API implementation.

Let's go over some examples for base URL.

PayPal uses API dot Paypal.com.

Ticketmaster uses API Ticketmaster.com and Uber uses developer dot Uber dotcom as the base URL for their

APIs.

One thing that you will observe here is that none of these APIs are being exposed over the w-w-w subdomain

for the API provider.

For example, PayPal will never expose its API on w-w-w dot paypal.com.

It is suggested that you do not use the world wide web subdomain for your APIs.

The approach that is taken is mostly to create a subdomain.

For example, PayPal has created the subdomain API and using its base domain.

Similarly, Ticketmaster is using app subdomain and Uber is using the developer subdomain.

API app and developer are the most commonly used subdomains for APIs, but there is no hard and fast

rule that tells you what the subdomain name should be.

You can decide on other subdomain names as well if you like to some organizations take this a step further

and create a separate base URL domain instead of using their primary domain.

For example, Walmart does not use walmart.com.

Instead it uses Walmart Labs.com for their API.

Another example is Expedia, which uses Expedia central.com for all of their APIs.

The suggestion is that if you have an option, then go for a dedicated domain for your APIs.

Next, let's talk about products.

Products are also known as packages.

The idea behind the product in the URL path is that you can logically group resources under APIs.

Let me explain it with an example.

Let's say you work for a large organization that is building multiple APIs.

Each of these APIs can have a dedicated endpoint and each of these APIs can be built by different teams

independently.

The product in the path is optional.

In other words, if you work for a smaller organization that does not have many teams working on the

API, then your API will look like this.

So the key point to keep in mind is that with products, the APIs can be managed independently by multiple

teams.

The other benefit of having this logical grouping is that it makes it easier for the client to understand

the APIs and the resources.

The API exposed by Ticketmaster uses the product in the URL path.

This is how the URL looks like for their discovery APIs.

This is for account API and this one is for Partners API.

Now I'm not sure why they did it.

Maybe they have multiple teams working on these APIs independently or maybe because it makes more sense

to group the resources under these headings.

My suggestion to you would be keep this product based grouping in mind when you are designing your domain

APIs.

Next one is the version an API may be thought of as a software product, and for software you need to

manage multiple versions at the same time.

So your API may have two or more versions actively being used by your API consumers.

By having this version in the path, it makes it easier for the users to switch between the versions.

So let's say your user is on version one and you release a new version of your API, which is version

two.

Now version one is still supported so the API consumer can switch to the newer version on their own

schedule rather than being forced to use version two on the day of its release.

Version in the path is optional because there are other ways to manage versioning in APIs.

You can use headers and query parameters.

I will be covering versioning in great details in a later lecture.

Last part in the URL is the resource and ID.

If you do not specify the ID, then the endpoint URL can be used to.

Preyed on the collection of resources.

If you provide the ID, then the operation is carried out on the specific resource identified by the

ID.

Let me explain it with an example.

Let's say we have to execute get on this Expedia endpoint URL.

Now if you do a get here, a collection of properties will be returned.

But if you specify the property ID in this URL, then that specific property will be returned as a response

to the get on the endpoint URL.

Last thing I want to cover in this lecture is the commonly used term root URL.

Root URL represents this part of the URL, so up to the version without the resource information.

So an API without the product definition will have this as the root URL for the API.

Time to summarize, in this lecture you learned about the endpoint URL for an API.

The first part of the URL is the base URL, which has the domain for the API.

It is suggested that you set up a separate domain or a separate subdomain for your API's.

Second part is the logical grouping that represents the products or the packages.

It makes it easy for multiple teams to manage their APIs independently by having the product in the

URL path.

Third part is the version.

As an API provider, you will have to manage multiple versions of the API and with the version in the

URL path, the API client will have the flexibility to switch between different versions of the API.

Next one is the resource, which is the resource on which the API action will be carried out.

And then the last one is the ID, which is the identity of a specific resource.