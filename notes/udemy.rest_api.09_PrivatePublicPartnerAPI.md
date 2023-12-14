---
id: rd2i300k16a3afa6ueu02zm
title: 09_PrivatePublicPartnerAPI
desc: ''
updated: 1699966380063
created: 1699964577729
---
Types of API.

At the end of this lecture, you should be able to describe the three different types of APIs and you

should be able to explain how these three different types of API differ from the API management perspective.

Let's start with some common terminology.

An API provider is an organization that exposes APIs.

Here are the examples of organizations which act as a provider API.

Consumers are the entities that consume these APIs.

There are three different types of API consumers.

The first type of consumer is the private or internal consumer.

These consumers are part of the same organization that is building and exposing the APIs.

Second type is the public or external API consumer.

These are outside of the provider organization.

The third type is the partner API consumer.

These consumers are trusted by the API provider.

Based on these three different API consumer types, there are three different types of APIs.

The private or the internal APIs are used only by the development teams within the provider's organization.

The public or the external APIs are meant to be consumed by the public domain application developers.

These public domain application developers consume the APIs from their applications and websites.

Partner APIs are used only by the trusted partners of the provider's organization.

One important point to note is that there is no difference in terms of the implementation of the API

for these three types.

The difference is in how these APIs are managed.

The private APIs exposed by an organization are used by the internal development teams in that organization.

These teams build applications that may be used from within the organization.

They may build applications or websites in the public domain that are used by the customers of the organization.

One thing to notice here is that this private API is on the edge of the organization and the reason

is because these APIs need to be accessed from outside the organization.

But there may be a scenario where these APIs are not consumed by the public applications.

In that case, these APIs don't need to be exposed on the edge of the organization and they may be available

only from within the enterprise network for that organization.

The Public API is always placed on the edge of the enterprise's network.

These APIs are consumed by the applications built by the public domain developers.

These applications may be mobile applications available on the app stores, or it may be websites available

on the end user's browser.

The public domain developers are not fully trusted by the API provider.

As a result, there are multiple levels of security placed on this public APIs.

The API provider organization exposes the partner APIs to its trusted partners.

These trusted partners have the app developers who may build applications for use from within the partner

network, and these applications are the ones that use the partner APIs.

At the same time, the partner developers may build applications in the public domain, and these public

domain applications may also use partner APIs exposed by the API provider.

As the owner of the API.

The provider organization needs to make a decision on what all API capabilities will be made available

to each type of consumers.

This is purely a business decision in general.

All of the features and capabilities are provided to the internal developers.

Many of the features may be made available to the partner API consumers and the least number of features

may be made available to the public domain API consumers.

At the end of the day, this is purely a business decision.

As I mentioned earlier in the lecture, there is no difference in the coding or the implementation of

the three different types of APIs.

The difference is in how you manage these APIs.

So as the API provider, one has to think through how the API security will be implemented, how the

documentation of the API will be carried out and made available to the app developers, how the access

requests will be handled and how the SLA will be managed.

Let's go through the details of each one of these.

The private or the internal APIs are consumed by the trusted internal developers within the provider

organization.

So for these API consumers, although security is needed, it's not the biggest concern.

I've seen basic auth and token based proprietary schemes in use for the internal APIs, whereas the

partner and public APIs are made available.

Two developers who cannot be trusted for these APIs.

Security is the biggest concern.

Key secret And OAuth security schemes are commonly used for these APIs.

My suggestion is to adopt a standard security scheme such as Key Secret and OAuth across all of the

APIs.

I will be covering API security in details in a later section.

Irrespective of the type of API, the app developers will need documentation for the API in order to

code their apps against it.

The private and the partner APIs are in a controlled environment so the API provider can share the details

of the API with the private and the partner API consumers by way of internal websites and by way of

PDFs.

This is a common practice used by many organizations.

The public APIs, on the other hand, may be thought of as an uncontrolled environment.

The API provider may not directly know all of the public domain app developers using their APIs.

As a result, the documentation needs to be available on web portals, which are typically referred

to as the developer portal.

My suggestion is that as the API provider, one should publish all of their API documentation on a dedicated

developer portal.

Once the API is ready, the API consumer needs to request access to these APIs for the private and the

partner APIs that are in a controlled environment and ad hoc process like emails and internal ticketing

process is commonly used for the public APIs.

We're talking about an uncontrolled environment.

The request is initiated from the developer portal.

The request raised by the public domain developer is either automatically approved by the provider or

it may lead to the initiation of some kind of a manual process.

Irrespective of the API type, my suggestion is to expose the API provisioning workflow on the developer

portal.

I will be covering the provisioning workflow in the section on API management.

SLA stands for Service Level Agreement.

Let me give you an example of an SLA from API perspective.

This API is guaranteed to be up 99.99% of the time.

It is offering a maximum throughput of 20 calls per second.

What that means is that the API consumer can invoke the API 20 times per second without experiencing

any performance degradation.

The API consumer can get support from the API provider by way of emails only.

The SLA doesn't have to be the same for all type of API consumers.

SLA tiers are typically defined and each of the API consumers are placed in one of these tiers.

For example, the highest level of SLA tier may allow the internal developers to make unlimited calls

per second to the API, whereas the lowest level may allow only 20 calls per second for the same API,

and this lowest tier is assigned to the public developers.

Another important aspect from the SLA perspective is the runtime management of the SLA.

The provider of the API must continuously monitor the key performance indicators for the API to ensure

that they are providing the quality of service they have committed.

By way of the SLA tears, the suggestion is to define and manage the SLA for all APIs, irrespective

of whether they are private, public or partner APIs.

Time to summarize, in this lecture you learned that there are three types of APIs the private or the

internal APIs which are consumed by the teams within the API Providers organization.

Then there is a public API which is consumed by the public domain developers, and there is this Partner

API, which is consumed by the trusted partners of the provider organization from the implementation

perspective, there is no difference between these three APIs.

The difference is in how these APIs are managed.

There are certain considerations that need to be kept in mind while managing these three different types

of APIs security documentation of the APIs, access request management and SLA management.

You will learn the details of each one of these in the section on API management.