---
id: 7kntef47g28n2qr6dooxq39
title: 28_Walkthrough
desc: ''
updated: 1699966834018
created: 1699965111204
---
In this demo.

I'll walk you through the steps you will need to carry out in order to create APIs for vacation resource.

We will create two APIs, one for getting the vacations and the other for creating new vacation packages.

There are three files of interest in this lecture.

The first one is the vacations dot JS file under the API slash v1 sub folder.

This file has the code that defines the API route and the request processing logic.

It is based on the express package, so if you are new to NPM express package, I suggest that you go

through the documentation available at this link.

Next file is the vacations dot js file under the models folder.

This has the schema definition for the vacations package.

Everything in Mongoose starts with a schema.

The third file is the Index.js file that has the code for launching the API listener.

It's a full blown NodeJS application and you will see it in action.

Now I'll walk you through the model for vacation package.

As you can see, I'm creating a vacation schema.

It's a mongoose schema.

The very first attribute is name.

The idea here is that typically travel agents give some small name to the travel packages or vacation

packages.

For example, if a travel agent is creating a package for Bahamas, they may simply call it Bahamas

1000.

Now, although Mongoose already is creating the primary key for us, we are also using name as one of

the indexes or a primary key.

So there is a uniqueness constraint on it.

Next thing is the description.

As you can see, this is also required.

And whether it's a resort or a cruise type vacation, that's the type.

Now there is always a need for a number of nights.

So we are saying that the minimum number of nights in a vacation package is one, a maximum is 31.

And then I would suggest that you go through this.

It's pretty easy to understand there is one attribute called valid till The idea of this attribute is

that not all packages will be available for all times.

They will be a date after which the package will not be available.

So a package will be available only till valid till date.

The API implementation is available in the vacations dot JS file.

The get verb will lead to the retrieval of documents from the MongoDB instance from the vacations collection,

and the post verb will lead to the addition of a document to the MongoDB collection.

In this file.

As a best practice, you will see that there are three constants defined.

The first one is the resource name.

This is what represents the vacation package, the resource vacations.

There is a version for the time being.

Just ignore this version.

We will talk about it in a later lecture.

And then Uri is a combination or a concatenation of the resource name and the version.

The very first route that we are creating here is the route for getting all the packages that are active.

And by definition, an active package is the one which has a valid till date set greater than today's

date.

So any package which can buy is a valid package.

The route that we are adding here is for get and it will be available under slash v1 slash vacations

and this will be our Uri.

Create the route by using the route dot get console log, say get vacations.

This is the criteria that we are setting for executing our query against the MongoDB.

Here we are saying that select all documents for which the valid till is set with the date that is greater

than or equal to today's date.

So when you say new date, it's today's date and then we are simply saying execute the query.

When we execute the query, we will set the callback method.

If there is an error connecting to the database.

For example, we are simply sending back a return code of 500 by doing a post status 500.

If everything works out fine then we are checking how many documents we have received.

If we receive zero documents then we are sending back an Http code of 404 which is not found.

And then we are sending back a response which is an array if there are certain number of documents received.

So let's go ahead and test this API.

Now to test, we will launch the NodeJS app by executing Node Index.js.

This will launch the API listener and to test switch to Postman and click on this plus symbol, provide

the URL which will be http localhost 3000 slash v1 slash vacations hit enter and here are the vacation

packages that we have received from our MongoDB instance.

So here we are setting up a route with post method.

The data for the vacation package is expected to be received in the request.

Body dot save method is called for saving the data received in the request body.

If there is an error, we are sending back a response code of 400 with the error received from Mongoose.

If everything is good then I'm sending back what I received from Mongoose.

Let's go ahead and test this code.

In order to test it.

I'll use the data that I have here.

I'm simply going to copy this.

And then postman select post provide the URL, go to body, click on draw, select Json and paste it.

So I have the data here.

But one thing that we will see here is that the name is Bahamas 1000.

If I try to insert this, we will receive an error because this has to be unique.

That's the index we created.

So I'm going to change this to 2000.

Now click on Send.

And as you can see, a new row has been added.

Let's go to MongoDB Atlas and check our collections.

And as you can see in the vacations collection, we have four documents now.

And here is the recently added document with the name Bahamas 2000.

I suggest that you try out this API on your own now.