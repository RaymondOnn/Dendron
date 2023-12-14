---
id: w4u6gnbp0iwmhwz8jwmr9s9
title: 53_APIManagement
desc: ''
updated: 1699967705961
created: 1699965861265
---
Introduction to API management.

In this lecture, I'll provide an overview of the activities related to API management.

There are two infrastructure models supported by the API management platforms.

I'll discuss both the models at a high level.

If you google what is API management?

The most common answer you will get is that API management is the process of publishing, documenting

and overseeing APIs in a secure, scalable environment.

There are multiple activities within the scope of the API management that will be carried out by the

API provider activities such as API lifecycle management, productivity enhancements for application

developers, securing the APIs, traffic management analytics, productization of the APIs and monetization.

I will be covering each of these in details in the next few lectures.

There are multiple off the shelf API management platforms that may be leveraged for carrying out these

activities.

Please note that the list of API management platforms that I'm showing here is incomplete.

There are two models supported by API management platforms.

The first one is the agent based and the second one is the proxy based.

Let me explain what the agent based model looks like.

So you have your enterprise network, which is the secure environment.

You have your APIs implemented on, let's say, NodeJS, WebSphere application server and even on your

Enterprise service bus to securely provide access to these APIs.

There is a gateway on the edge of the enterprise API management policies gets applied on the APIs within

the servers, hosting the API through some kind of an agent that is provided by the API management platform.

The access by the application is through the secure gateway.

So this is one of the models.

In the proxy based model, the API management platform sits on the edge of the enterprise.

The proxies are defined on the platform and the calls coming to the proxy are routed to the API implementations.

In the trusted network.

Policies are applied to the proxies, not to the API implementations.

When an app makes a call to the proxy, the policy is applied and if everything is good then the call

is routed to the backend or the API implementation.

The most common policies applied to the proxies are the security policies and traffic management policies.

Another important element of the infrastructure is the developer portal.

The app developers can access the developer portal to get information about the APIs, to raise requests

for access, to get support for the APIs.

Lectures in this section will cover the details of these activities and also I'll use some API management

platforms as example to show you how you can carry out these activities using some of these API management

platforms.

Quick disclaimer I'll be using the API platform as examples.

I'm not being paid in any shape, way or form by any product vendor to do that.

I'm not recommending any platform by way of these examples.

I'm using certain tools as examples because they offer free trials and products may be adding features,

products may be changing.

So you have to check on your own.

The product features intent is not to carry out a comparative analysis of these products and for selecting

an API management platform, please do your own research.