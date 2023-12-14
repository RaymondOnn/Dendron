---
id: fdak9aqw9ya8j3qvxz9umjw
title: 14_UniformInterface
desc: ''
updated: 1699966522292
created: 1699964719452
---
In this lecture you will learn about the rest architectural constraint uniform interface.

By the end of this lecture, you should be able to describe the uniform interface constraint and you

should be able to discuss the four guiding principles related to this constraint.

The uniform interface constraint states that the client and server share a common technical interface.

There are two key words in this statement technical and interface.

Interface defines the contract between the client and the server, whereas the keyword technical implies

that there is no business context for this contract.

There are four guiding principles that are used for defining the contract between the client and the

server.

Let's go over those four guiding principles.

The first guiding principle is related to the identity of the resource.

Individual resources are identified in the request.

The rest APIs expose resources and these resources are exposed by way of request URLs which are used

by the API clients.

These request URLs or URLs identify the resource that is getting manipulated by the API client.

Let's say there are two car instances, each uniquely identified by their Vin number, so the request

URL may have the format similar to this, where the Uri contains the Vin number for the car and hence

identify the resource manipulated by the API client.

Here is an example for the Acme travel.

With this URL, the API client intends to get the reviews for the vacation package identified by an

ID equal to 21.

Second principle is related to the representation of the resources the API client receives the representation

of the resource.

For example, the client may receive data that describes the car and the client can then use the data

in the resource representation to carry out the manipulation of the resource.

So, for example, if the client has received the car resource with Vin number one, two, three, four,

five, it can use that information to carry out further manipulation of the object.

And this is the URL that the client can use for deleting instance of the car.

One important point to note regarding the representation of the resources is that server may manage

the data for the resource in a database or other persistent storage.

So the format of data on the server side is not necessarily the same as what is desired by the client,

and the client may request the server to provide the data in Json, XML or some other format so the

server has to transform the data from its internal representation to the desired format.

Number three Self-descriptive Messages the metadata.

The idea here is that the client and server exchange metadata in the request and responses.

For example, when the rest client invokes the API endpoint, it can send the information on formats

that it can accept.

In the accepts header, for example, the client may set the accepts header to application slash Json

to inform the server that it can accept Json responses.

Same way the API server sends back metadata to the rest API client.

Here are some examples of the information sent in the Http header by the rest API.

The content type will hold the content type of the payload in the response.

Http status code provides information by way of the Http codes.

Host has the information on which host the response is coming from.

The fourth principle.

Hypermedia suggests that the server not only sends the response data, but it also sends back hypermedia

that the client can use for discovery.

Let me explain it with a visual rest.

Client sends the request data to the server server sends back response data, and in addition to that,

it also sends back actions which are hypermedia links for discovery.

This behavior of the RESTful API server is referred to as the hideous hypermedia as the engine of application

state.

I will be covering hideous in a later lecture time To summarize.

In this lecture I covered the rest architectural constraint referred to as the uniform interface.

This constraint suggests that there should be a contract between the client and the server, and there

are four guiding principles that.

The contract has to follow.

The first one is that individual resources are identified in the request URL.

Number two, representation of the resources may be used by the client to manipulate the resource state.

Number three the messages are self-descriptive.

There is metadata contained in the request and responses that can be used by the client and the server.

And number four server can not only send back responses, but hypermedia links that can be used by the

client for further discovery of resources.