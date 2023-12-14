---
id: qsaucjl04ousodi0sbobbff
title: 23_SetupApiURI
desc: ''
updated: 1699966719781
created: 1699964959520
---
It may appear, design decisions.

The idea behind this lecture is to apply all of the knowledge that you have gained in the last few lectures.

By the end of this lecture, we would have discussed and designed the Acme API URL, the resources,

the search action and the associations between the central Acme API resource vacations with other resources.

Before I start with the design process, let's think about who is the consumer of Acme API.

Now if you look at the value chain, the direct answer would be that app developer is going to be the

consumer of the Acme API.

But the success of the API will be defined by the end user's experience.

So we cannot ignore the end user when we are thinking about the design aspects of the Acme API.

We need to keep in mind the end consumer's experience and then we need to ensure that we are providing

all of the required capabilities in our APIs to the app developer so that the app developer can build

desired consumer experiences.

Acme API base and route URL.

Recall from the lecture on API URLs.

This is the format of the route URL.

I've decided to use a subdomain for the APIs.

API dot acme.com will be the base URL for acme.

Since the Acme vacation API is not that complex, it doesn't have too many resources.

I've decided not to use the product and since we are creating the first version of the API, we'll go

with version V1.

Don't worry about this version.

You will learn more about it in a later lecture.

So the route URL for acme API will be api.acme.com/v1 acme API resources.

Recall from one of the previous lectures that the names of the resources should be nouns.

In the case of Acme APIs, I expect the resources to be identified as the API will evolve.

At this time.

We know of one of the resources that is central to Acme business and that resource is the vacations

resource.

To get the vacations packages using the API, you will be using this as the URL endpoint as a best practice.

We will not use verbs such as get vacations.

If an API consumer wants to carry out an operation on a specific vacations package, they can use this

as the endpoint.

The difference is that in the second we are providing the ID of the vacation package, travel platforms

like Acme provide such capabilities so the end users can carry out search on the portal or applications

and find the desired vacation packages.

As the Acme designer, we need to think about an API that will provide this capability to our app developers.

Let's say we have commonly seen Destination as a criteria for searching for vacation packages.

So we may expose this endpoint wherein the API consumer can send the destination and we can return the

vacation packages for that destination.

This is good.

If only destination was the criteria for searching the packages, but commonly the end users search

the travel and vacation packages by not just the destination but by using multiple other criterias such

as price, number of days etcetera.

In that case, this will not work and we need to have a more generic search capability as a result.

As the Acme API designer, I have decided to provide an action with the name search on the vacation

packages.

The criteria for the search will be provided by the consumer in the form of query parameters.

So this is the search capability will build in the Acme APIs.

As an Acme API designer, I need to think about other resources with which vacations package resource

is associated with the end.

Consumers can upload their vacation pictures, so there is a photographs resource which will be associated

with the vacations resource.

The idea will be that the app developers should be able to show some pictures from the destination of

that vacations package to give an idea to the end user the kind of experience they will get by buying

that vacations package.

Another resource that comes to mind is the reviews.

After taking the vacations, the end users will write their reviews and assign stars to the vacation

packages.

The app developer will need the capability to carry out actions on the photographs and reviews.

So as the.

Designer.

I've decided to expose this end point for managing the photographs.

The app developer will be able to pull all the photographs for a particular vacation package, such

as vacation package with the ID 1001 and display it in the application.

Similarly, for reviews, I've decided to provide the capability where the app developer will be able

to act on a specific review for the vacation package.

So in this example review with ID number ten is getting deleted for the vacation package ID number 1001.

Time to summarize in this lecture I've walked you through the steps I've taken for designing the foundational

elements of Acme API.

The Route API for Acme APIs will be API dot Acme.com v1.

As I've decided to go with a subdomain rather than a separate domain version is set to a fixed version

at this time to V1 as it's the first version of the API.

The central resource in the vacations API is the vacations package for search.

I've decided to go with a more flexible criteria based search action wherein the app developer can provide

multiple search criterias in the query parameters.

And I've identified two additional resources, photographs and reviews that are associated with the

vacations package resource.