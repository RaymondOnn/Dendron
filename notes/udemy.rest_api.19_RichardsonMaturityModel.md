---
id: lmtbnolk14wifmx9i9sl5y1
title: 19_RichardsonMaturityModel
desc: ''
updated: 1699966624573
created: 1699964831705
---
Richardson maturity model.

By the end of this lecture, you should be able to discuss the four levels in the Richardson maturity

model, and you should be able to estimate the maturity level of arrest architecture.

In one of the previous lectures, I touched on the Richardson maturity model.

With this model, you can measure the restfulness of the architecture.

There are four levels in this model and the architecture is evaluated to check out its level.

If the architecture is at level zero, then it is considered to be not in compliance with the rest practices,

whereas the highest level is level three, wherein the architecture qualifies to be considered as fully

compliant restful architecture.

Next, I'll discuss the details for each of these levels.

In Richardson model, the level zero is referred to as the Swamp of Box.

Box stands for plain old XML.

The idea is that enterprises that are at level zero have adopted RPC and they are using XML as the data

format for requests and responses.

Typically, these organizations have built their service oriented architecture using the soap standard.

I like to refer to this level zero as RPC.

The idea is to check how the RPC is implemented in an organization.

Enterprises at level zero use Http as the transport.

They mostly use the Http get and post verbs and they do not depend on the other http verbs such as put

or delete.

The endpoint URLs look like verbs.

For example, get customer data and most such organizations do not follow a set standard for naming

the RPC endpoints.

Recall that Acme Travel has implemented RPC endpoints and these RPC endpoints are exposed over Http.

The data format used for the request and response is XML.

Most of these endpoints are using Http get.

With this analysis we can say that Acme is maturity level for Restfulness is at level zero.

Level one of Richardson maturity model is also referred to as resources.

Resources are a representation of real world object.

The suggestion is that the web should be considered as a collection of resources rather than as a collection

of URLs or endpoints.

The RPC server manages the state of the resources, and the RPC endpoint is used by the RPC client to

manipulate the state of the resource.

Let's think of these resources for Acme travel.

Now Acme is in the business of selling vacation packages, so the obvious resource that comes to mind

is the vacation package.

The vacation package resource may be associated with other resources such as reviews and photographs.

In addition, there may be other resources such as hotel deals and car rentals.

Now there may be many more resources apart from these, but these are the common ones from Acme perspective.

Level two of Richardson maturity model is referred to as the Http verbs.

The Http protocol defines multiple methods or verbs in the context of rest API.

The common Http methods are the get post, delete and put.

There are additional methods which I have not listed here.

The idea is that the resources are manipulated by way of using the right Http verb.

The traditional web applications use either get or post for all of their web services.

So if that's what the API implementation is using, then that is not at the maturity level.

Two resource may be manipulated by way of four operations which are referred to as the Crud operations.

Here.

C stands for create, R stands for retrieve U stands for Update, and D stands for delete.

Let's look at it with an example.

The Acme Rest server may expose an endpoint for manipulating the vacation resource on receiving an Http

request.

With the post method, it will create an instance of the vacation resource on receiving a request on

the get.

The rest server will retrieve the instance of vacation and return it back to the caller on receiving

a request on the put method.

The rest server will update the vacation instance and on receiving the delete method call The rest server

will delete an instance of the vacation resource specified in the request.

Level three is referred to as the hypermedia.

Who the rest client not only receives the representational state of the resource, it also receives

the hypermedia links that the client can use for further manipulation and discovery of resources on

the server.

This is the concept of haters that you have already learned in one of the previous lectures.

If the rest architecture follows all the practices discussed in the previous levels and it also sends

back links as part of the response, then that rest implementation has achieved the highest level of

maturity, which is level three.

In this lecture I covered the Richardson maturity model.

Rest architecture at level zero is the one in which the remote procedure calls that is RPC is implemented

by way of Http and XML.

The architecture is said to be at level one if it is exposing domain objects as resources by way of

well defined Uris.

The architecture is considered to be level two if the operations for the resources are exposed by a

common endpoint, an appropriate Http verbs like get post, put and delete are in use.

Architecture is said to be at level three if the server is not only sending back the resource state

or data, it is also sending back the links that the client can use.