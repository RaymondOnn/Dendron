---
id: b67dytarci3ebpcjktzrnq9
title: 33_VersoningTheAP
desc: ''
updated: 1699967060832
created: 1699965287801
---
Rest API versioning.

At the end of this lecture, you should be able to explain what is API versioning and then you should

be able to describe the common patterns that may be used for building versioning in rest APIs.

I'll go over multiple examples to show you how these APIs are implementing versioning.

Before I talk about Rest API versioning, I just want to make it clear that the versioning here is not

referring to source code versioning.

It is about rest API product versioning.

The suggestion is that API versioning need to be managed like any other product version.

For example, the Ubuntu developers manage multiple versions of the Ubuntu software and they have a

very well defined roadmap for their product.

Similarly, the API provider must have a clear versioning roadmap for their product.

The idea is that at some point you will retire the old versions and at that point you do not want to

impact your API consumers and you would like them to move to the new version.

So provide them a roadmap.

And during those periods of time when you will have multiple versions available to the API consumers,

you will have to provide a way to the consumers to connect to those multiple versions.

Let's say you have two versions of the API available to the consumer.

The consumer should be able to connect to any of these versions.

The idea here is that if you have a new API consumer, they will always use the latest version of the

API.

Whereas for the consumers who have been using the earlier version, they will take some time to migrate

to the newer version.

As a result, you need to support multiple versions of your APIs.

Parallelly.

As a designer of the API, you will need to decide on two aspects.

The first one is how will the consumer specify the version?

And the second one is what will be the format for the version information.

As an API designer, you have three options that you can use for version specification.

First one is you may ask the API consumer to provide the version information in a custom header.

For example, you can create a new custom header and every time the API consumer invokes a call on the

API endpoint in the header, they will set this custom header.

Second one is to use a query parameter.

This query parameter will be used by the API consumer for all calls.

The third option is to use the URL path element such as slash v2 slash products.

This V2 here is the version.

This is the approach that is most popular.

There are three common version formats.

First one is you can use the date of release as the version, or you may go with a major dot minor version

setup.

For example, 2.82 is the major version, eight is the minor version.

Or you may go with a single number as the version.

These last 2nd May be prefixed with the letter V to indicate that it's a version.

Let's go over some versioning example.

Twilio, which is a communication API, uses the uses the URL path and the date for the specification

of the version.

This is the dev portal for Twilio.

Just click on base URL and you will find that the Twilio API requires you to use this as the base URL

for all resources.

And this date here is the version of API that the consumer is going to use.

Next example is Amazon APIs that use date as the version format, but instead of using the URL path,

it uses query parameters for the specification of the version.

This is one of the STS Assumerole APIs.

So if you scroll down and take a look at the example, you will find that this API requires query parameter

version to be set to a date, which is the release date for the API.

Next example is the movie database that uses the URL path and a number as a version as of July 2020.

Version four is the latest version, but the API consumer can use an older version, which is version

three.

So as you can see here, this API is using the number in the URL path for versioning.

Next example is Uber APIs, Uber APIs use the URL path and a number, but this number is prefixed with

the letter V.

Here is an example of the version two of the Uber API, wherein it requires V two to be added in the

URL path.

Stripe uses a very interesting approach for versioning of their APIs.

I am calling it hybrid approach.

In this approach they have a major and a minor version.

They specify the major version in the URL path and V number format, and the minor version is specified

in the Http header.

As a date.

Now, I'm not sure why Stripe developers have decided to go with this kind of complex versioning mechanism,

but I'm sure they must be having some good reasons.

In this example, you can see that they are using V1 in the URL path for version one, and the consumer

also has to specify a header stripe dash version which is set to the date.

As an API provider, you need to support multiple versions of your APIs.

In order to do that, you need to provide a way to the consumer to specify the version of the API they

want to use.

And there are three commonly used options http header, query parameter and URL path in which the API

consumer can specify the version.

Then you need to standardize on version format.

Three common options for that are to use date of release as the version using two numbers in the format

major dot minor or a single number to represent the version.

These number based version formats are commonly prefixed with the letter V.