---
id: i4xmpctzs4gsccrnyfd8yy5
title: 15_Statelessness
desc: ''
updated: 1699966545923
created: 1699964740636
---
Rest architectural constraint statelessness.

By the end of this lecture, you should be able to explain what statelessness mean and you should also

be able to explain the benefits of building stateless Rest API servers.

This constraint suggests that the client must manage its own state, thus making the server stateless.

Before I talk about the statelessness, let's look at the design of a typical web application.

A web application receives requests from the web client, and when the web client first connects to

the web application, the web application creates the data structure to manage the state of the client

in some kind of a session store.

Subsequent calls from the same client leads to the retrieval of the state information by the web application

from the session store.

The processing of the request is done by the web application by looking at the current state and the

request data received from the client.

For every client that is connecting to the web application, the web application manages the state independently.

This practice of managing the state in the server is not considered a good practice for building restful

APIs.

A restful API server does not manage the state of the clients.

Each of the client connecting to the RESTful API server manages its own state.

The state management implementation is left to the client application and is not governed or defined

by the rest API server.

Each client is responsible for managing its own state data.

The benefit of this approach is that it simplifies the implementation of the restful API.

It allows the API server to easily scale horizontally and it reduces the resource needs for the API

server.

The requests received from the client applications or rest clients are self-contained and what that

means is that server receives all of the state information that it needs for processing the client request.

Each request received by the server is treated as an independent request without regards to any previous

requests from the same client or from other clients.

Let's go over the key learnings from this lecture.

The rest API server does not manage the state information for the clients.

Each of the client is responsible for managing its own state.

All requests that are sent by the client to the server are self-contained.

In other words, the request contains all of the information needed by the server to process the request.