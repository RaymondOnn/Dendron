---
id: k4kpd79446ymnbsczesxfp5
title: 20_WebappVsRestAPI
desc: ''
updated: 1699966646173
created: 1699964855663
---
Web application versus rest API architectures.

By the end of this lecture, you should be able to explain the architectural similarities and architectural

differences between web application architecture and rest API architecture.

The World Wide Web may be thought of as a massively scalable distributed application.

It has been in existence for over 25 years now, and it has matured a lot in the last 25 years, from

static web pages to feature rich functionality available on the web pages.

It is not a surprise or a coincidence that web application architecture forms the basis for the rest

API architecture.

All of the proven practices and technologies that have been used in the web app development are used

for designing and developing rest API architectures.

Let's compare and contrast web application architecture and Rest API architecture, Web app and Rest

API server.

Use the client server as the foundation for their architecture.

Typical approach for building the two involve using the layered approach.

Both the restful server and the web app can achieve higher performance and scalability by using caching

code on demand, and higher tiers are supported by both the web apps and rest API servers.

From the uniform interface perspective, the web application does not have any hard and fast rules for

the setup of the URL for the resource, whereas in the case of restful API, there are strict rules

on how the Uri should be defined and then there are best practices that should be followed for designing

the Uri, for the restful resources for state management.

The web application may or may not manage the state of the client.

It all depends on the requirements.

But if there is a need to manage the state, then the web application server will manage the state locally

in some kind of a persistent storage.

Now recall, in the case of rest APIs, the server will never manage the clients.

State client is responsible for managing its own state and when it sends the request to the server,

it also provides the state related data as part of that request.

When it comes to the use of Http methods, the web applications typically use only the get and the post,

whereas the rest APIs must use the appropriate Http method for the Crud operation on the resource.