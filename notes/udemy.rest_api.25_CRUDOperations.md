---
id: l7n2i65t6yiwmc95obisru8
title: 25_CRUDOperations
desc: ''
updated: 1699966761699
created: 1699965036474
---
Rest API Crud practices.

By the end of this lecture, you should be able to explain how the request and responses are set up

for the Crud operations on API.

You should also be able to describe the Http status code returned from the API implementation for the

Crud operations.

Recall that in one of the previous lectures I talked about the use of appropriate http verb for Crud

operations.

So you would use post for create, get for retrieve, put for update and delete for delete.

In addition to these Http methods or verbs, there are a couple of other Http methods that you should

be aware of.

These Http methods are not very commonly used when it comes to the rest API implementations.

The API server will receive a request from the client over the Http post method to create a resource.

Let's go over what's needed in this request from the client.

The API server will need to provide the resource endpoint.

For example, if the resource is the vacations package.

In the case of Acme, then this will be the Uri.

If the resource is a sub resource, for example, the review, then the Uri will look like this wherein

you have the vacations with a specific ID and then the resource review.

The data for the resource will be passed in the body of the post request.

The designer of the API may also require the API client to set up headers in the Http post request,

but this is not so common.

The headers set in the http post request may be standard headers or some custom headers.

Next, let's talk about the response to a Http post.

The http API server must always send back the status code in the Http header.

If the creation of the resource is successful, then it should send back to zero one created.

If the server finds that the data sent in the request for the resource is incomplete or is invalid for

some reason, then it should send back 400 missing required field or bad request.

If the server has a problem or issue in processing the request, then it should send back a 500 series

message.

For example, if the database is unreachable, it should send back 503 database unreachable for the

message body.

The API implementation can do one of the two things.

It may return the new resource instance.

For example, if the data is in Json format, the body will contain the resource representation in Json

format or the API implementation can simply return a link to the newly created resource instance.

For example, if the new resource instance is created with an ID of one, 423, then this is the link

that the server will return in the body for retrieval of resources.

The API client will invoke the endpoint provided by the server with the get method.

The request will be carried out against the endpoint for the resource.

This endpoint will return a collection of all the resources, whereas if the ID is specified in the

path for the Uri, then only one instance of the resource will be returned.

So in this case the vacation package with the specified ID will be returned.

The API designer can optionally allow query parameters in the request header.

The API implementer may require the API client to provide standard or custom headers.

Typically the request body is optional in case of retrievals.

The response returned from the get operation will have the Http header set to 200.

Okay.

If the server was able to retrieve the requested resources, if the client provided information that

could not be processed by the server, then an appropriate 400 request will be received by the client.

For example 404 in case the resource is not found.

If the server had a problem with processing the request, then it will send back a 500 series error

in the body.

There may be collection of resources or there may be a single instance of the specific resource requested

by the client for updating the resource you may use put and patch.

You would use port.

If all of the attributes of the resource are getting updated and patch, as the name suggests, is used

when some of the attributes of the resource need to be updated.

For large objects, it may be more performant to provide support for both put and patch.

The idea is that you don't want the large object data to be sent through every time.

For updates with Patch, you can only send part of the data that needs to be updated.

Here is an example of a patch update wherein we are updating the vacations package with ID 121 updating

just the valid till attribute.

The request for the put patch will require the endpoint with the identity of the resource.

The data to be updated may be provided as part of the request parameters or it may be provided in the

body.

For the potent patch, the API may require the API client to set the standard or custom headers.

The API server may implement, put and patch in several ways.

The response to put and patch has the Http header in which the status code is set to 200.

If the updated resource is in the body, it has a status code of 201 created, but the body data is

optional and it may send back to zero for no content.

If there is no content in the body and then if there are issues, then 400 series error or a 500 series

error will be sent back.

The body in the response depends on the Http status code received from the API server for deleting the

resource.

The http delete verb will be used and the request will be sent to an endpoint which will be either a

collection of resources or a specific resource.

Parameters may be specified as the selection criteria for the resources to be deleted and body is not

needed and Http headers depend on the needs.

The API server will provide a response to the delete by way of Http code 200 success.

If the deletion of the resource was successful and the body contains the data for the deleted resource,

it will send back 204 No content.

If there is no content in the body and delete was successful.

If there is any kind of failure then an appropriate 400 series or a 500 series status code will be sent

back.

The body in the response depends on the status code returned by the API server.

One common question that I get asked a lot of times is can I use post for updating?

And the answer is yes, you can.

In fact, Twitter uses just get and post for all their APIs.

What I have suggested to you are commonly adopted practices.

These are some guidelines.

You can develop your own practices and standardize across all your APIs.

Let's take a look at Twitter's API.

I'm on Twitter's developer portal and here you can check out the API reference for the various APIs

that are available on Twitter.

So I'm looking at the manage account settings and profile API and you will find that there is only get

and post in use.

Twitter does not use put, patch and delete for all of those actions it uses post.

So for example, this is a partial update of the account.

This will have the same effect as if they were using Patch.

This is where they are creating new resources for deleting.

They use slash destroy.

So using the URL path elements, they're defining the various operations to carry out on the resource,

but just using the post instead of using the other verbs.

So Twitter developers have developed their own way of building the APIs as long as they follow the same

standard and guideline for all their APIs, there is no harm in doing so.

As an exercise, I suggest that you go through the API documentation for Uber and try to answer the

question How does Uber use Http headers in their APIs?

Let's go over the key points from this lecture.

You should use appropriate Http methods for all your APIs.

You may use post for creation updating and deleting the resources, but if you do so, maintain consistency

across all your APIs in the response.

Always send back appropriate Http status codes as discussed in this lecture.