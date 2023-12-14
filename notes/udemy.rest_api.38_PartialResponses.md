---
id: uuxokin75btf3oxpie67hxu
title: 38_PartialResponses
desc: ''
updated: 1699967196924
created: 1699965470248
---
Partial responses.

By the end of this lecture, you should be able to explain how the client can control the granularity

of the data sent in the API response, and you will also be able to discuss the API implementation options

for supporting partial responses.

Typically, the rest API server will expose a common endpoint for all rest clients.

For example, a web application which is creating a browser based interface will connect to that endpoint,

and a mobile application with a smaller screen size will also connect to that same endpoint.

Since the browser based application has more screen space to display the data, the browser based application

will show all of the details on the screen.

For example, in the case of vacation API endpoint, it will receive all of the data for the vacation

which may be displayed on the browser, whereas the same endpoint when invoked from the mobile application,

will get all the data, but it will display only the packet summary due to the smaller screen size.

In effect, the mobile application client uses part of the received data as it cannot display all of

the data.

As a result, it's leading to the inefficient use of resources on the mobile client.

Same size fits all.

Doesn't work well from the mobile client's perspective.

In other words, use of common endpoint exposed by the API server is not working out well for the mobile

client.

As a best practice, the client should be in control of the response data.

What that means is that there will be a common endpoint for all clients, but the client should be able

to tell the API server what it needs and the API server responds back with only the attributes of the

resource that were requested by the rest client.

Here we say that the client is in control of the granularity of the data sent by the server.

The benefits of this approach to the mobile client is that it leads to better performance of the application

and it leads to optimal usage of resources on the mobile phone from the server side, the server doesn't

have to create multiple endpoints to support different API client types.

It can support a common endpoint for all consumers.

As a developer of the API, you will need to support partial responses in your API implementation.

Now, there are two ways to do it.

The first one would be to custom build the partial response support in the logic, and the second one

is to use GraphQL, which was developed by Facebook and released to open source in 2015.

Think of it as a different way of building APIs.

I will be covering the custom build partial response support in this lecture.

There are two ways which are commonly used for the field specification by the API client.

First one is where a single query parameter holds an expression that identifies the fields.

These fields are also known as the projections.

The second way is in which multiple query parameters are used in this one.

In addition to the projections, there are additional query parameters that provide support for building

filters for the fields.

Next, I'll give you an example of each one of these.

LinkedIn requires the API consumer to provide value for a single query parameter by the name fields.

The value is set to an expression which is interpreted by the LinkedIn API implementation, and only

the requested fields are sent back to the API consumer.

Here is an example.

If the fields is set to first name and last name, then only the first name and the last name attribute

of the resource will be sent back to the consumer.

The expression set up has some rules that need to be followed to get more information on these expression,

I suggest that you check out the LinkedIn dev portal, click on the Development API Guide API concepts

and then select field projections.

Here you will find information on how the consumer needs to set up these expressions.

So here is a simple example where the fields are set to ID bar and baz, and this is how the response

looks like.

The expressions allow you to also specify child or associated objects and the fields that you would

like to get for those child objects.

So here is an expression that will send back the ID from the resource object.

Now Resource is referring to another object by the name Baz, and that has an attribute B which will

be returned in the response.

So as you can see here in the response, the two requested attributes are being returned.

If you're interested in implementing your API using this kind of projection mechanism, I highly recommend

that you go through the.

LinkedIn documentation.

My next example is the Meetup API that uses multiple query parameters for defining the projections in

addition to the field's query parameter.

The Meetup API also supports two additional query parameters only an omit with the only.

You may restrict the fields or attributes of the resource that you need or you can use Omit to omit

the fields out of all of the attributes for the resource.

So depending on your resource data, you have to decide whether an expression based approach will work

for you or whether an approach like this will work for you.

In case you are new to meetup.

Meetup is a website where you can check out the information on local events to check out their API documentation.

Just go to meetup.com, slash meetup, underscore API and look for filters and you will find result

field and filters.

This is where you can find more information on the use of the various query parameters that are discussed.

An API typically returns the complete resource representation to the client by way of supporting the

partial responses.

An API can send back only the fields that the API consumer is interested in.

That way, the API client is in control of the granularity of the data returned to it.

This has multiple benefits, especially for the mobile applications.

It provides better performance for mobile application and the resource usage on the mobile phone is

optimized.

The API provider has to put together a way by which the API consumer specifies the fields that they

are interested in.

This can be done by way of field projections and filters.