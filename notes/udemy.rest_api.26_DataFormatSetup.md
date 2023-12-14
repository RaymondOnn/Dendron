---
id: 7b16h0o67tp2o3kqou09l8d
title: 26_DataFormatSetup
desc: ''
updated: 1699966784380
created: 1699965060393
---
Rest API data formats.

All modern rest API implementations support Json format, but there are good reasons why so many APIs

support multiple data formats.

In this lecture you will learn three techniques that you can use for building APIs that support multiple

data formats.

I will be using the example of MSNBC, PayPal and Weather Channel to show you how they have built APIs

that support multiple data formats.

By now you are very familiar with the API flow as an API designer.

One question that you will have to answer is what should be the data format for the request and the

responses?

The answer depends on your requirements, but one question that one should ask is what would give the

most value to my customer?

Now there is a general misconception that Rest API must use Json.

That is actually not true.

You can have a rest API that can support many, many formats.

The common formats that are used for rest API are Json, XML and CSV.

My suggestion is that if possible, build support for multiple formats.

If that is not possible, then think of the multi format support as a feature for your API for future

implementation.

In other words, build your API with the single format support, but make it flexible enough so that

in future you can support additional formats.

The API client should be in control of the format that it is requesting for.

In other words, if your API supports multiple data formats, the client should be telling what format

they would like the response to be in.

So here, for example, the client is requesting the response to be in Json format.

The server may respond back with the Json response if it supports Json, or it may indicate to the client

that it does not support the format that client has asked for.

Now there are multiple ways in which the API can be implemented to support multiple formats.

Let's go over the options.

Query parameters may be utilized for the specification of the format.

For example in the MSNBC API for breaking news, client needs to specify the value for the output parameter.

Check out the breaking api.com here.

You will find that the API supports Json and CSV.

Let's learn a little bit more about this API.

Go to learn news API, click on parameters and here in the parameters you will find there is a parameter

called output that can be set by the client to Json for getting the response in Json format or it can

be set to CSV.

The second way of supporting multi data formats is by using the Http headers.

The standard header except is commonly used for specifying the data format.

So for example here the accept application slash Json is indicating to the server that the client is

requesting the response to be sent in Json format.

This is how PayPal is implementing the multiple data format support in their API.

Check out the API reference, click on API request and then check the http request headers.

Accept.

And this is where the API client will set the format.

Now interesting thing you will note is PayPal is saying where format is Json and the reason they are

saying it is because today they support only Json as the format, but future proofing the API.

So in future they may support other formats as well.

Third way of doing it is by way of resource format suffix.

This one is a little tricky.

The client needs to specify the format by using a dot notation and the format.

So day dot Json.

If the client requires the data to be provided in XML or CSV, they'll put de dot XML or de dot CSV

where the company uses this scheme for supporting multiple formats.

The API portal is simple looking but has a lot of good information, so let's check out their 15 minute

forecast.

Now, if you look at this API, you will find that the client needs to use the API endpoint with dot

Json as the suffix for the resource.

Let's take a look at another API daily forecast API.

Click on default and you will find day dot Json.

I suggest that you explore these APIs on your own irrespective of how you decide to implement support

for multi format in your API.

Here are some suggestions in the Http header.

Send back the status code of 200 if the requested content type is supported and you could process the

request successfully.

Send back an Http status code of 415 if.

European implementation does not support the client requested format.

And then obviously if there are issues, then you will send back an appropriate 500 series response.

Always set content type as http header in the response.

Last but not the least, ensure proper documentation of all the supported formats so that your customer

that is the app developer can take advantage of this feature in your API.

As the designer of the API, you need to decide whether you would support multiple data formats in your

API.

If you decide to do it, then there are three ways in which you can support multiple data formats.

Query parameters.

Http headers or using the resource format suffix.

The client will use the mechanism to request the data in specific format in the API implementation.

If you receive a request from the client for a format that your API does not support, then you need

to return http 415 as the status code, which indicates that the API implementation doesn't support

the requested data format.