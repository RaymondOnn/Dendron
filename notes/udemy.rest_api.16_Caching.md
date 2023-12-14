---
id: xnxi33jqntp17btd0fiurev
title: 16_Caching
desc: ''
updated: 1699966569917
created: 1699964757157
---
Rest architectural constraint.

By the end of this lecture, you should be able to discuss the caching architectural constraint, and

you should be able to describe the Http headers that the server can set up for controlling the caching

behavior on the client.

In the lecture on Statelessness, you learn that the API server does not manage the state of the API

client.

The API client is responsible for managing its own state.

This architectural pattern has some challenges.

The first challenge is that it can lead to increased chattiness of the application.

In other words, the application may have to make multiple calls to the rest API server to get the resource

representation.

The size of the request data sent by the client to the server will increase as a result of the fact

that the client has to send the state data along with the request data.

And overall the performance of the application may degrade because of the increased chattiness and the

size of data being sent from the client to the server.

The caching constraint suggests the use of caching to achieve higher scalability and performance of

rest applications and server caching counterbalances the negative effects of stateless server implementation.

There are multiple points within the API path length which can take advantage of caching.

The developer of the API may decide to put caching in front of the database.

By doing so, the developer can get better performance from the database read perspective.

Developer can create a local cache within the server to cache the data from the database and also to

cache the responses that it will send to the rest clients.

These caches are in control of the API designer or developer.

An application using the rest client can also cache the responses locally.

This way, the application can avoid making calls to the API server and use the data from the local

cache.

Overall, the application level caching will lead to better performance of the application and higher

scalability for the API server between the application and the API server.

There are various intermediate devices such as proxies and gateways.

These proxies and gateways also have caches built into them, and these caches can further improve the

performance and reduce the complexity of the client and server from caching perspective.

The developer of the API does not have full control on how the caching is carried out by the application

and the intermediaries.

The API server can send cache control directives in the responses to control the caching behavior on

applications and the intermediaries.

A well behaving application and intermediary will respect the cache control directives to manage the

local caches.

Not all responses can be cached, for example stock prices.

In scenarios where responses cannot be cached, the server should explicitly mark those responses as

Non-cacheable for rest over http.

The cache control directives are specified in the http headers in the response.

The http header cache control is used for specifying the caching policy that is applied to the responses

received by the rest API client.

The value in the cache control header consists of multiple directives which are separated by commas.

Some of the cache directors have an optional argument.

Here is an example of the cache directive.

Cache control public comma max dash age.

So here we are specifying to cache control directives public and max age.

The No store directive informs the API client not to cache the responses.

Private directive informs the API client that the response data is for a specific user and can only

be cached on user's device.

Public public directive informs the API client that the resource may be cached in any cache.

Max Dash Age is the time in seconds for the cache resource to expire.

In other words, the cached resource data will no more be valid after one 20s and the API client will

need to clear the cache after 1/22 and get the latest data from the server.

There are few additional.

Cache control headers.

The expires header is used for setting the exact date time when the resource will get expired.

The last dash modified is set to a date and time when the resource was last changed.

And Etag is a special header which is set to a unique identifier associated with the response.

It lets the caches be more efficient and saves bandwidth.

I suggest that you read through these headers on your own to get the details of how they are used.

In this lecture I covered the rest architectural constraint caching.

It is suggested that caching be used for scalability and performance.

The server controls the caching behavior on the client via cache directives for rest over Http.

These cache directives are specified by way of Http headers.

Cache control is the Http header that allows the specification of the caching policy in terms of cache

directives.

Then there are additional headers related to the caching behavior.

Expires last modified and etag.