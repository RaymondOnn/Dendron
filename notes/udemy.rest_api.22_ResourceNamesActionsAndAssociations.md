---
id: ss69c6h2slti4c5xx0x7c3u
title: 22_ResourceNamesActionsAndAssociations
desc: ''
updated: 1699966689506
created: 1699964927586
---
Rest API practices.

In this lecture, I'll cover three important concepts resource naming actions and associations.

Let's take a look at the names of some of the resources exposed by some example APIs.

The Yelp API exposes a resource by the name businesses and you can carry out an operation on a specific

business by using its ID.

LinkedIn exposes a people resource.

A specific person is identified by people slash ID.

The movie database has the resource slash list and you can get a specific list or operate on a specific

list by providing its ID.

You will observe that the names of these resources are all nouns.

Early on, when we were doing just RPC and not following best practices, we used to have verbs instead

of resource names as nouns.

For example, you would say get businesses or get people or get list in rest APIs.

You do not use any verbs.

You will always use nouns for the names of the resources.

Another thing that you will observe here is that Yelp is using businesses, which is a plural of business,

whereas the movie database is using slash list, which is singular.

It is not using lists.

So you will find a mixed use of plurals and singulars.

But most of the APIs that I have seen use plurals.

So my suggestion to you would be to use plurals instead of singular.

So the idea is to follow a practice which is followed by most of the designers in the lecture on Richardson

maturity model, you learned about the Crud operations that can be carried out on the resources exposed

by APIs for these Crud operations.

You would invoke the endpoints for the API using an appropriate Http method.

But what if the operation that needs to be carried out on the resource is not a Crud operation?

For example, what if you have to carry out a calculation or you need to carry out some kind of reporting?

In that case, you can define the action using a verb.

Let's take a look at an example of an API that exposes an action.

This is the dev portal for the Spotify, and if you click on the search API, you will find that the

API is exposing a slash search and is using query parameters for passing on the search criteria to the

search action exposed by the Spotify API.

One important thing to note here is that this particular action does not apply to any specific resource

in the API.

Let's go over some more examples of actions.

Uber provides an action to estimate the price of a ride by way of slash estimates slash price.

Twitter lets you look up the friendships collection.

So what you are seeing here are actions that are carried out on the specific resources.

So when you have a requirement for defining an action on a particular resource, then define that action

as part of the resource hierarchy.

Let's talk about resource associations here.

I'm referring to relationships between various resources within a domain.

For example, Orders resource may have a collection of items, resource or in a social media application.

People have followers.

These relationships may be identified within an endpoint URL for the API.

For example, to find a specific follower that a person has will have a URL like people slash ID to

identify a specific person and then followers and then to get the details of a specific follower will

have the ID of the follower.

On the E-Trade platform.

People can manage their brokerage accounts and these accounts have transactions.

Now this is the dev portal for E-Trade.

And if you look at their API for less transactions, this is how it looks here.

We are saying list all of the transactions under this particular account ID if you want to get the details

of the transaction, check out the list transaction details.

In this API, you will see that instead of getting the list of transactions, we are getting the details

of a specific transaction identified by the transaction ID.

Here are two more examples.

Expedia provides API for getting the amenities for specific room types within specific properties.

The weather company has an API which has a that allows latitude, longitude and days to be specified

for getting the weather predictions.

So as you can see here, these can become very big with too many Nestings.

My suggestion is to restrict the nesting of resources to a maximum of three levels.

And if you do have a requirement where you need to specify relationships between multiple resources,

consider using sub queries to avoid this deep nesting.

Time.

To summarize in this lecture you learn about three important concepts.

Resource naming actions and associations.

Resource names should be nouns, and it is suggested that you use plurals instead of singulars.

Actions can be verbs and actions may be standalone or applied to specific resources for building associations.

Avoid nesting beyond three levels.