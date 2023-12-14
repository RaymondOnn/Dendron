---
id: 0z2eli3s6pg6iot9dvf66d6
title: 18_CodeOnDemand
desc: ''
updated: 1699966605741
created: 1699964803422
---
Rest architectural constraint code on demand.

By the end of this lecture, you should be able to explain what is code on demand and you should be

able to describe the idea behind hiatus.

I'll show you how PayPal uses hiatus for its APIs.

The code on demand constraint suggests that the server can extend the functionality of the client by

sending it code.

This is an optional constraint.

So what that means is that if you have implemented your rest API by following the first five constraints,

then your API qualifies as a rest API even if you haven't implemented code on demand.

Now, if you think about it, this is similar to how the World Wide Web works.

Let me explain what I mean by that.

When a browser connects with the web app, the web app responds by sending back HTML.

And in addition to the HTML, the web app can also send back code in the form of JavaScript flash apps

or Java applets.

This code sent by the server gets executed on the browser and that's how the web app is extending the

functionality of the browser, which is the client to the web app.

Now if you think about rest APIs, the only difference is that rest server send back the response instead

of the HTML.

This response may be in different format depending on the implementation of the rest API.

In addition to the response, the API server also sends the code which then gets executed on the rest

client, thus extending the functionality of the rest client.

This is the concept behind code on demand.

It stands for hypertext as the engine of application state.

The idea behind hiatus is similar to code on demand.

Consider this web application browser can connect to the web application and receive HTML.

This HTML may contain hyperlinks.

These hyperlinks are displayed on the browser window.

The user can click on the hyperlinks to carry out actions on the same website or on other websites.

In the case of Rest API, the browser is replaced by the rest application and server is replaced by

the rest server.

Instead of sending the HTML, we have the response coming back from the rest server in multiple formats.

This response may contain hyperlinks and like the browser, the rest application or the rest client

can use these links to carry out actions on the resources managed in the rest server.

Let's take a look at hiatus by way of an example.

Let's say the rest API client needs the information on the vacation package with ID 123.

The rest server would look at the information available for the resource requested by the API client

and then it will send back the vacation package details in addition to the vacation package detail.

The rest server will also include some links such as a link for adding a review and for booking the

package.

The rest API client, which may be thought of as the rest application, may use the link to add a review

for the vacation package so the actions that can be taken on the resource are made available to the

rest API client by the rest API server.

Now let's assume that the package is sold out and this information is available to the rest server.

In that case, the response may not include the link for booking the package.

So in other words, the actions that can be taken by the rest client are controlled by the rest server.

There are multiple benefits of using hiatus.

The first one is that since the server is closer to the resource, it is aware of the full resource

state and decide on the actions that can be taken on the resource to get the full state of the resource

from the client and the client will need to make multiple calls to the server which can lead to inefficiencies.

The second benefit is that server may change the Uri for the resource without breaking the client.

And third one is that server may add new functionality over a period of time.

Now these links are being sent to the client does not mean that the client has to use these links.

The client may also evolve at its own pace and use the links to offer new functions and features in

the application.

This is the developer portal for PayPal.

PayPal supports hiatus.

There is a search box here, so just type here hiatus and you will see a document explaining how hiatus

is being used by PayPal.

PayPal sends back hiatus links in API responses.

This is an example.

Of the response with the element links in it.

This links element is an array and it can have one or more link objects.

This first one here is the link to the payment transaction that led to the response.

And this link can be used to get the information about that payment transaction using the get method

and the provided link.

The second one is for carrying out refund of the transaction.

So using the link provided and with the method post, the consumer can execute a refund transaction

for this particular payment transaction that led to this response.

This last one here is to get the parent transaction information for this transaction.

I suggest that you go through this document to understand how PayPal uses Https.

In this lecture you learned about the optional architectural constraint code on demand.

The code on demand suggests that the rest server can not only send the responses, but also can send

code to the client to extend its functionality.

The code sent by the rest server is executed on the client.

The idea behind Radius is that the response can contain hyperlinks that the client can use for manipulating

the resources on the rest server.