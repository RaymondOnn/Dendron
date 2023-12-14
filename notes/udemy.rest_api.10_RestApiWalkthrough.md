---
id: arwr9p4y2mymwq0jmck1ofl
title: 10_RestApiWalkthrough
desc: ''
updated: 1699966399009
created: 1699964599989
---
Example API walkthrough.

In this walkthrough of the Ticketmaster Developer Portal, I'll show you how Ticketmaster is exposing

its public and partner APIs, how they are managing the access to these APIs and the cells for each

type of APIs.

At the end you will also see an API in action.

I highly recommend that you please walk along and try out the Ticketmaster APIs on your own.

Ticketmaster has built capabilities related to the management of e-tickets for different events such

as concerts, sporting events.

ET cetera.

They expose these same capabilities for managing e-tickets by way of their APIs.

Let's check out their developer portal.

Just go to Google and look for Ticketmaster Developer API.

Click on the Ticketmaster Developer API.

On this developer portal, you will get all the information you need as the app developer for using

the Ticketmaster APIs.

For example, you can read through the information on how to get started with the use of these APIs.

Here is the data model.

Think of these as the resources for the Ticketmaster Rest API implementation.

Ticketmaster offers public and partner APIs by way of this portal to use their public API.

You need to get your own API key, so click on Get your API key.

Create the new account, provide all this information here and you will receive an email with the approval.

This approval process is automated and then you can use the credentials to access the Ticketmaster APIs.

There are two tiers of partner APIs available for Ticketmaster.

There is a distribution partners and the certified partners.

I won't go into the details of the difference between these two.

To become Ticketmaster's partner, you need to fill up a form or send an email to Ticketmaster to get

the access.

So if you would like to check out what they need from their partners, click on the link here and then

click on Become a Partner.

You need to provide all this information.

Someone at Ticketmaster will manually review the information and then grant the access to the partner

API.

These APIs have a very well defined SLA under the getting started section.

Look for rate limit and you will find that the Ticketmaster APIs allow a maximum of 5000 API calls per

day with a rate limitation of five requests per second.

But at the same time, the app developers may request for the increase of the quota and the rate limit.

Next, I will show you a feature which is very popular with the app developers.

As an app developer, you would like to see the API in action before putting together even a single

line of code.

Now I have already received my API key.

I've already registered, so next thing I'm going to do is click on the API Explorer and try out some

APIs here.

My API key is already populated because I'm logged in and let's go ahead and try out their discovery

API that allows you to search for events.

Now these are the request parameters that I can set.

Let's set the end date and just try out the execution of this API.

Although you saw there are quite a few parameters.

I've just set the one parameter.

So here you can see all of the events that are available and you can get the details of these events

by just clicking on these and it will provide you all the information on the events, including links

to the images for the event.

And you can look at this data in Json format as well.

I suggest that you try out these APIs on your own.