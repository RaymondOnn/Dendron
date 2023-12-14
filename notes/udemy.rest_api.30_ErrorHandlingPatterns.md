---
id: qd0m90hi3bdv4d0xhluyrau
title: 30_ErrorHandlingPatterns
desc: ''
updated: 1699966878348
created: 1699965177246
---
And a response from it.

There are two objectives for this lecture.

First, I would like to show you how to set up the error responses.

And second is to introduce you to a pattern called response envelope pattern.

The error information sent back to the client is meant for use by the application developer.

This information is used by the application developers to define the runtime behavior of their application

at the same time.

Some developers may want to log this information to some kind of database or persistent storage, and

the reason these developers may do it is for root cause analysis and for creating reports for their

applications.

This is a common practice.

Here are some guidelines for designing your error response.

The first one is that the error response should be informative and it should be actionable.

What that means is that the developer of the application should be able to figure out from the error

as to what actions need to be taken in order to fix the issue.

For example, the API may send back the name of the field, which is a required field, and the app

developer can use the name of the field to highlight on the interface, the field that needs to be fixed

by the user.

And that's just one example.

The error response format should be simple.

And the third one is that it should be consistent across your APIs.

The idea behind this is that if you're using different error message formats across your APIs, the

developer will have challenge dealing with different APIs with different error response schemes.

So keep it consistent across all of your APIs.

Here are some suggestions on how to make these messages informative and helpful to the application developer.

We can provide links to the API documentation right In the error message, we can provide hints in the

error message to address the issue and we can provide the messages that may be presented directly to

the end user API status code.

The idea here is that like the Http status code, which is for standard Http statuses, APIs should

also have their own API status codes or application status codes.

The idea is that these status codes are numeric in nature and these numeric status codes can be used

by the application developer or the API client to put together the application logic for managing these

errors.

For example, in the vacation APIs, I have set up an error with the error code 7002, which indicates

that the required field vacation description is missing.

These application status code or the API status codes must be managed like any other development asset.

The idea is that we do not want developers to create new error codes for the same kind of status or

the same kind of error.

Let's go over the vacations API error template.

This is a simple template that has a message and a timestamp.

This message is a high level message meant for the application developer.

It's not meant for the end user.

Timestamp is for the time captured on the API server when the error occurred.

Then there is a Http method which is set to post get put on which the endpoint was invoked.

These two fields are important to be captured as the application developer may log this information

for reporting as well as for carrying out the root cause analysis.

Error list is an array of error objects that provide information on one or more errors encountered as

part of the API processing.

Then there is a received payload.

This payload is the request payload.

Now this should be used only for debug purposes.

The idea here is that received payload may have some sensitive information that you may not want to

put in your logs.

So this is expected to be used only for debugging purposes.

So let's go back to errors.

Now.

Errors is an array of error objects.

Each of the object has a code, some text message, which is the error description.

Then there are hints for the developers on how to address the issue or the error.

Then there is an info attribute which is linked to the documentation for the API on addressing the error.

The idea behind the hints and the info is to assist the developer as much as you can with resolution

of the error.

Let's take a look at some sample errors for the vacation API here.

As you can see, the code is set to 7001.

The text indicates that the field with the name name is missing and then there is some hint there and

an information link that provides information on the required fields.

This is another error 7002, which indicates that the required field number of knights is missing.

And then again it.

Gives a hint and provides the information link.

At this point, I would suggest that you pause the video and take a look at this standard error message

that PayPal uses.

Compare it with the Vacation's API error response that I am suggesting.

Response Envelope.

Now, this is a concept which I have seen being implemented by very few APIs in this scheme.

The response always consists of two parts the status and the payload.

The status is a set of fields that describe the status of the call, including any errors.

So if I were to design the vacation APIs with response envelope pattern, I would have said the status

as this.

And in case there is no error, the error list will be null.

But if there are errors then the error list will contain all the errors and the payload will be null.

This particular scheme is in use by Expedia.

Let me show you how it looks when the product API definition.

If you click on format response, you will see that there are two parts in the response the entity and

the errors.

The entity is the actual resource representation and the errors is a list of errors returned by the

Expedia API.

If there are no errors then the errors list is null.

In this lecture you learn how to set up the error responses.

One important point to note the error responses are meant to be consumed by the application developer.

It always helps to standardize the error responses across all your APIs include application or API status

code that the app developers can use for building.

The logic for managing the errors provide as much information in the error as possible so that the developers

can easily understand what they need to do in order to fix the errors.

I also talked about the response envelope pattern, which suggests that you can always send back the

response in two parts status alongside with the payload.

The API client checks the status to see if there is a payload or not.

If there is an error, then there is no payload.

If there is no error, then the payload has valid resource representation.