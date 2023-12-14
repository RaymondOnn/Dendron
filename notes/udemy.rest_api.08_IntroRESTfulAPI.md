---
id: uofglxl9r5c9qfl7milnm5t
title: 08_IntroRESTfulAPI
desc: ''
updated: 1699966356651
created: 1699964498646
---

Rest API concept.

At the end of this lecture, you should be able to answer the question What makes an API restful?

All real world objects or resources may be described by way of attributes.

For example, a car may be described by way of the make model year and the Vin number.

And this Vin number gives car a unique identity.

In software systems, these attributes are managed in some kind of persistent storage.

It may be a Dbms or it may be even a NoSQL database.

There are certain attributes of the object that may change over a period of time.

For example, the owner of the car may change once bought the new car, so the owner attribute is set

to wins when sold the car so he is no more the owner and since he sold the car to Kathy.

Now Kathy is the owner.

All of this state data changes are captured in the persistent storage.

So what we are saying here is that the representational state of the car is managed in the database

and they may be multiple instances of cars which are uniquely identified by way of their Vin number.

Each of these cars have different values for the attributes and different owners.

Now think of a system that can query this database.

The system may ask the database for details of, let's say, Vin number one, two, three.

For this query, the database will respond by sending back the representational state of the car with

Vin.

Number one, two, three.

What you just learned is the foundational concept of rest, the representational state transfer.

The owner tracking application received the representational state for the car with Vin number one,

two, three from the source database.

This representational state data may be in any format.

For example, it may be a collection of name value pairs.

It may be in the database record format.

This internal representational state is converted to other formats.

For example, some kind of a logic can be put together to convert it into HTML, which is consumed by

the browser client, or it may be converted to CSV, Json, XML, which are the popular formats for

mobile applications, web applications and partner application integrations.

But those are not the only format it can be converted to.

This representational state may be converted to any format such as Jpeg, PDF, Excel, etcetera.

For the sake of discussion, let's remove the HTML format.

Now, if we replace the logic with the rest API, then this is a good representation of the restful

API.

From the data formats perspective.

What this tells you is that a rest API is not tied to any specific data format.

In fact, the same instance of a restful API can convert and send back the representational state of

a resource in different formats based on the API client's needs.

Indeed is a job posting site which also offers a rest API.

This rest API manages the Job resources, A restful API client interested in getting the information

on the job may call the get operation with the specific job ID and request the API server to send back

the response in XML format.

The RESTful API implementation gets the job information from the internal database or some persistent

store converts it into XML and sends it back to the rest client.

Similarly, the rest client can ask for that information to be sent back in Json format.

Let's check out the documentation for the indeed API.

Just type on Google search bar.

Indeed.

Developer API.

Here is the link to the API.

Click on the job search API and let's go through the parameters needed in the request.

These are the request parameters and here you will find that the client will need to set up the format

that they are interested in.

By default, the format is set to XML, but the API supports XML and Json.

Let's take a look at the response data format.

So depending on what the client has requested, the API implementation will respond back either with

the XML data for that job or if the client has requested the data to be in Json format, then it will

respond back with this kind of a Json data.

So this is just one example of how an API can support multiple formats.

The indeed API that we just looked at uses Http as the communication protocol.

All modern APIs use Http as the communication protocol and we call such APIs, Http rest APIs.

And the reason is because the rest API style itself is not tied to Http.

You can build rest APIs with other.

Protocols as well.

Now it's quiz time.

In this lecture and the last few lectures, I have covered quite a few concepts related to restful APIs.

I would like you to answer this question.

What makes an API restful?

Mark The correct and incorrect choices suggest that you pause the video and make an attempt.

All right.

Hopefully you have got all the correct answers.

These are the right choices.

Rest APIs, follow a set of design principles, rest API, follow the rest architectural style rest

APIs expose resources on which the rest client can carry out operations.

And these are the incorrect choices.

Rest APIs are not tied to any specific technology.

Rest API does not define any standard for request or response payloads or how the APIs should be built.

Rest APIs can use any communication protocol and any data format.

It is not tied to Http and Json, which is a common misconception.