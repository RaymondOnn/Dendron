---
id: w9id230ppa6r5hpjcg46anw
title: 24_ApiRequestRowAndStatusCode
desc: ''
updated: 1699966737316
created: 1699964993716
---
Rest API contract and Http status code.

By the end of this lecture you will be able to explain the http API call flow.

You will have a fairly good understanding of the various parts of the API contract and you will understand

how Http status codes are used by the API server rest API call flow.

The API client needs the resource endpoint to which it needs to send the request data.

This resource endpoint identifies the specific resource managed on the API server.

The call needs to be made with the appropriate http verb, and the verb that the client will use depends

on the operation that it is carrying out on the resource.

The rest client sends the request data to the server.

This request data is consumed by the API server for carrying out the action on the resource.

The rest API server then sends back the response to the API client.

Client provides the request data by way of the query parameters.

The http header and the Http body.

The response is received from the server in the form of Http header and body.

The specifics for the request and the response need to be defined independently for each of the Http

methods, and these specific aspects are provided in the uniform interface, which is the contract between

the client and the server.

The API developer needs to provide the resource endpoint and the sub query parameters that are supported

by the API endpoint from the request perspective.

For each of the Http methods that are supported by the API, the API designer or developer need to provide

the Http headers that it supports and this can be the standard headers and the custom headers for the

request body.

The API designer will require the client to provide request data in certain format and so the contract

will specify the data format, for example, Json or XML, and it also will provide the request schema

for the response.

The server sends back the Http header and http body in the header.

The most important part is the Http status code.

This Http status code is used by the API client to evaluate the success or failure of the call apart

from the status code.

The server may also set some standard or custom headers that the API client will use.

The body will have the data format which will be specified by the API designer and a response schema.

The API client will need the data format and the response schema to interpret the data that it has received

in the body.

In this lecture I'm going to cover just the Http status code, but in later dedicated lectures you'll

learn about all of the practices that you need to know in order to set up the uniform interface or the

contract.

I'm pretty sure that at some point in time you have seen the screen which indicates that the page that

you are looking for has not been found.

The number 404 is the Http status code.

An Http server always sends back the status code and the status response text in the header.

The status response text is an informational message related to the status code.

The Http status code is a three digit number and the first digit in the three digit number represents

the classification of the message.

There are five such classifications.

Let's go over it.

If the status code starts with number one, then those status codes are meant to be just informational

status code.

Starting with two represents success status code starting with number three are redirection directives.

Status code starting with number four represent a client error and status code.

Starting with number five represent a server error.

Let's go over some examples here.

Let's say the client invokes an endpoint that does not exist.

In that case, the client will receive the status code of 404 not found.

Another example is where the client sends a request to create the resource and the server successfully

created the resource.

In that case, the server will send back 200.

Okay.

Another example is let's say the server received the request for creation of the resource, but it could

not reach the database.

In that case, the server will respond back with status code of 500 internal server error or database

unreachable.

The Http standard codes are documented in.

In the RFC 2616.

If you are interested in learning more details about these codes, I suggest that you look through this

document here.

I've listed the 41 commonly used Http status codes as an API developer or designer.

You must make sure that you are not inventing any new codes.

You must use the standard Http codes for your rest APIs.

Etsy is an e-commerce web portal which specializes in handmade craft and crafts supplies.

It offers APIs to developers.

Let's check out the documentation for their APIs.

You can get the listing of their products by using a standard end point.

They support an output format of Json and Json.

P.

They support all of the four http verbs.

They have a standard response format which has this schema and here are the standard response codes

that they send out in response to all of the API requests coming to their API suggest that you visit

the site and go over the documentation and relate it to the concepts you have learned in this lecture.

Time To summarize, the API client must follow the API specification or contract to execute operations

against the resource endpoints.

In this lecture I provided you an overview of the API contract, which includes the endpoint and the

query parameter specification.

It also includes the request and response, http header and body specifications.

The response from the API server always includes the Http status code in the Http header.

This http status code is a three digit number and the first digit in this three digit number is a classifier.

This is a standard set of codes and the API server must always return back a standard code and should

not invent its own codes.