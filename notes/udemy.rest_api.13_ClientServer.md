---
id: n5t5lle4bmjxwet0v3xs3qv
title: 13_ClientServer
desc: ''
updated: 1699966463061
created: 1699964694817
---
Client server rest architectural constraint.

By the end of this lecture, you should be able to explain the benefits of the decoupled architecture

approach suggested by this constraint.

The client server constraint states that rest applications should have client server architecture.

This constraint serves as the foundation of rest architecture.

Now, in this statement we see two key words client and server.

The client is a component that requests the server for resources.

The server component serves the resources as a response to the client request.

Server may serve multiple resources and the server may serve multiple clients at the same time.

Client and server should not run in the same process space.

Let me explain what this means.

Traditionally, applications are developed by writing components in terms of modules, so you may write

a client module and a server module, and then you can package the client and the server module in a

single monolithic application which will run as a single process.

And then the client module can execute function calls on the server module.

So technically this is feasible, but it's definitely not a good idea as the client component and the

server component will be very tightly coupled and such tight coupling will lead to many challenges and

most times it may not even be possible to package the client and server in the same process to achieve

highest level of decoupling between the client and the server.

The client will make requests to the server over some kind of a network.

This way the client and server can change independently without impacting each other.

This scheme of things will continue to work between the client and the server as long as they maintain

the uniform interface or the contract between the two ends.

This decoupled architecture leads to what is known as the separation of concerns.

Let me explain it with an example.

The client and the server end have a different set of concerns that they need to address and manage

on the server end.

There is a need to manage security at multiple levels.

For example, data level security, network level security application or API level security.

Then there is a need to persist the resource data in databases or other storage schemes.

Then there is scaling needs.

When the load increases or decreases, the server needs to manage appropriate level of compute resources.

On the client side, there is a different set of concerns for example, how to manage the authentication

and authorization for the end user, or how to support multiple screen sizes or multi form factors,

how to build the application using appropriate technologies and platforms.

And there are many concerns other than these on each side.

The two ends can continue to independently manage these concerns without impacting the other end.

As long as the uniform interface between the client and the server is maintained.

Another advantage of the decoupled architectural approach is that the client and the server can evolve

independently.

Let me explain what I mean by this.

Let's say we have a server that supports APIs for desktop applications.

The resource data that is sent back to the rest client is in XML format.

This API became very popular and the developers of the application requested the API developers to support

Json format as well for their mobile application.

So as long as the uniform interface does not change the existing application will not be impacted and

the server will be able to support the same resource API for Json.

And similarly, in future there may be another request for the resource data to be provided in CSV format.

So as you can see here, the server can continue to evolve independent of the client.

And similarly, new clients may join the ecosystem with support from the server.

The client server architectural constraint provides the foundation for the implementation of rest APIs.

This architectural style is suggested as it leads to decoupling between the client and the server.

This decoupling has multiple advantages.

The client side and the server side can change without impacting the other side as long as the uniform

interface is maintained.

Another advantage is the separation of concerns each end has.

Different set of concerns that they can manage independently.

The client and the server can evolve over a period of time in an independent fashion.