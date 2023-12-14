---
id: d6xchldy0g22j0to1wpzw5w
title: 42_Security
desc: ''
updated: 1699967294439
created: 1699965559637
---
Rest API security.

Strongly believe that security is the most important aspect of designing and implementing APIs.

A well-designed API that follows all the rest practices and design principles can become a liability

instead of an asset.

If it does not implement adequate security measures to protect the enterprise assets, bottom line is

that API designers should be thinking about security from the very get go.

Otherwise, they are increasing the risk of hacking attacks against their enterprise.

In this lecture you will learn about various aspects that you need to consider when designing your API

to be secure.

What you see here is a typical application flow in an enterprise.

The application connects to the the data by way of multiple touch points, such as Gateway and the API

server.

An attack can occur on almost any of these points.

The attacker can attack the application and manipulate the data or steal the identity.

The attacker can look at the vulnerabilities, understand the vulnerabilities of the gateway, and that

way connect to the back end systems.

The attacker can in fact breach the firewall and go directly to the API server or the databases.

As a designer, you must consider all these attack possibilities, not just the APIs.

The best solution to protecting your API would be to prevent the attacker from reaching your API management

server or API itself.

So the bottom line is think about security at every touch point in the path length, not just for the

implementation.

Let's consider a mobile application and what the designer and developers of mobile application have

to think through when designing the security aspects.

A bank has a lot of data, and this data is very confidential in nature.

It has the customer personal data and also it has transactional data.

So typically banks have a way of protecting this data by way of strong firewalls.

Let's assume this firewall is impenetrable.

They have to come up with the a set of APIs which will be exposed to the mobile application.

In effect with this architecture.

What we have just done is we have introduced an attack surface.

Part of the infrastructure firewall through which the attackers can get access to the data.

So what can the attacker do?

The attacker can.

The attacker can steal the data.

They can manipulate the data.

They can carry out identity theft and they can launch a denial of service attack.

The attacker typically launches these attacks by exploiting some weakness or vulnerability in your API.

As a designer of the API, you have to think through all the possible type of attacks that can be launched

against your API.

In the lecture on functional attacks, I'll discuss some of the common attacks against the API that

you must protect your API from.

Another way in which the attacker can attack your API is by way of mimicking the mobile application

and making calls to your APIs.

In the design of your APIs, you have to think about how you would authenticate the incoming calls.

How would you make sure that the calls are coming from the mobile application and not from an application,

which is a rogue application written by an attacker or a hacker?

Authentication is the first thing that comes to mind when we think about security.

There are multiple ways of carrying out authentication.

I will be covering the most common ways the basic authentication, token based authentication and key

secret based authentication.

When a customer uses the mobile application, they are, in effect, interacting or invoking your APIs

from the perspective you need to make sure that whatever transactions are getting executed by way of

the API invocation are indeed authorized by the end customer.

And that's where OAuth comes into picture.

In this section.

I'll also be covering OAuth 2.0, which has become the de facto standard for implementing authorization

for APIs.

Last but not the least.

Security of data is another consideration.

API designer has to think about how they would secure the data.

Let's delve a little bit deeper into the security of data now.

Data security involves maintaining or managing measures to protect the data from unauthorized access

or theft and also maintaining the integrity of the data.

Although some of these aspects may not be in control of the designer, but the designer has to make

sure that these aspects are covered because API itself is opening the doors to this data and if it is

unprotected, then obviously API can lead to a lot of risk around the data security.

In other words, the data at rest, the data in the database servers and other infrastructure is outside

the scope of rest API design and implementation.

You cannot control that as an API designer.

You have to work with other stakeholders to make sure that data at rest is also protected.

On the other hand, the data in motion, the data that is getting transmitted from the mobile application

or any application to your API is within the scope of the rest API design and implementation.

And the solution is to make sure that you are always using TLS and Https for rest APIs.

The data will be encrypted and will be protected for from any unauthorized access or man in the middle

attack.

Another good practice around data in motion is do not use Self-signed certificates.

The lectures in this section cover three main aspects from a security perspective.

Authentication authorization and the functional attacks that may be launched against your API.