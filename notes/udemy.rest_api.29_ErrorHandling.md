---
id: 8rkh1ntphs21fuwe16d6sox
title: 29_ErrorHandling
desc: ''
updated: 1699966858779
created: 1699965132781
---
Rest API error responses.

By the end of this lecture, you should be able to explain the difference between the standard Http

errors and application errors.

You should be able to describe the various options available for you to send back detailed error information

to the API client and you should be able to describe the best practices for Http status codes.

Application Errors.

So far I have discussed how the Http header status, code and reason phrase are used by the API implementation

for sending status information back to the API client.

The three digit code in the status code and the information are standardized by way of RFC 2616.

Although the API implementation can use these header fields for application related errors, it is suggested

not to do so because these are standardized codes and phrases.

Instead, the API implementation can send back the error information in custom headers or the API implementation

can use the body for sending the error details.

The body should have a fixed error format which is defined by the API designer.

It may contain an application status code similar to the Http status code, and these status codes are

defined by the API designer.

It is suggested that the error format is standardized across all of the APIs.

Rest API Error Flow API client sends a message to the API server gets back the response.

If there is an error, then the API server needs to send back the information about the error in the

http header and the body on receiving the error.

The application logs the message and provides a meaningful message to the end user of the application.

The key point to note here is that the API implementation needs to decide how the error will be sent

back to the rest client.

Next, I'll go over various options you have available as the API designer on sending back the error

information to the rest client.

The first option is that the API implementation may send all of the error information in the Http header.

It can use the standard http header field status code and the reason phrase.

And in addition to providing information in these two headers, API implementation may introduce additional

custom headers.

This custom header can contain more information on the error.

The Etsi API developers have decided to go with a custom Http header for error information and status

code for the error code.

This is the Etsy developer portal and I'm looking at the API basics page here.

And as you can see here, this is the list of Http status codes that are sent back by the Etsy APIs.

And this is the header dash error dash detail where the API is setting additional details for the error

In option number two, all of the error information is contained in the body and the way it works is

that the Http status code is always set to 200.

Okay.

And error information is put in the body in standard format.

Older version of Facebook API used to use this approach at this time.

The newer versions have moved away from it.

I'm not surprised that I was unable to find any API that is using this approach.

In my opinion, it does not really align with the Http standard and you should not be using this approach

in your API.

The third option involves sending the error information in the header as well as in the body.

The way it works is the API client on receiving the response checks, the error code.

If the error code is a 200 series error code, then it interprets the body as the resource information

or resource link.

If the error code is 400 or 500, in that case, the API client assumes that there is error information

in the body.

The structure of the error information is standardized.

Uber uses this approach.

In fact, this is the preferred approach by most API developers.

Go to developer uber.com click on docs select write request select errors and here you will find the

information on how the Uber APIs are sending back error responses.

The Http status code is set.

And then this is the key here.

Error responses have a consistently formed Json body and this is the error format.

I suggest that you read through this page.

One question that I get asked all the time is should I use all.

Http codes.

And the response to this question is no, you don't.

In fact, you should limit the number of Http status codes that you will support with your API.

In my observation, well-designed APIs support on an average of no more than ten codes.

Here are the codes that I have seen commonly used by most APIs.

As the designer of the API, you need to decide which Http status codes would make most sense for your

API.

There are two reasons why I'm suggesting that you limit the number of status code support in your API.

The first one is that it will be hard for the API development team to manage the use of these status

codes consistently in the API code and it is very important for your API development team to maintain

consistency across all APIs.

The second is that it will be difficult for the API developers to handle the Http status code if there

are too many of those.

The Xperia APIs support 14 Http status codes in their responses.

This is slightly on the higher side, whereas Tomtom traffic API supports six http status code in the

responses which is on the lower side.

In this lecture you learned how you can send back the errors in the response.

There are three options that I have discussed.

The first option is wherein you send the error information only in the Http header.

In this option, you can also include a custom header for capturing the details of the error.

Second option is to use only the body for sending back the errors.

And the third option is to use the combination of Http header and body for sending back the error information.

The body is sent back in some kind of a standard format.

It is recommended that you don't use this option as it violates the Http standard.

Suggestion is to go with option three.

That is use of Http header in combination with the details of the error in the body.

Another practice that are covered in this lecture talks about the limiting of the number of Http status

codes supported by the API.

Suggestion is to limit it to ten.