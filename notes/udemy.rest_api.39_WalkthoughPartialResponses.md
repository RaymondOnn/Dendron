---
id: h2q9do9s30gwldw02st1ub5
title: 39_WalkthoughPartialResponses
desc: ''
updated: 1699967215307
created: 1699965491988
---
Demo of partial response support in APIs.

In this lecture, I'll walk you through the code for Acme API for models that support partial responses.

Acme is establishing partnership with multiple hotels around the world and the database of hotels is

growing.

The designers at Acme have decided to expose these hotels by way of an API.

The resource here is hotels.

Each of the hotel has some data which is quite big, and what is happening here is that since this API

will be used for mobile application as well as browser front ends, they would like to give the control

to the consumer or the app developer to decide what fields they would like to receive and the mechanism

for specifying the fields will be the query parameter fields equal to the comma separated list of the

fields that the consumer would like to receive.

To demonstrate the working of the partial response API, I'll carry out five steps.

First, I'll create a new collection in the Acme Travel Database.

We'll name this collection hotels.

Then we are going to switch the branch in the local repository setup to partial response, then update

the DB slash cloud db dot js file with the connection parameters.

After that, we'll populate the hotels collection with data by executing tests, slash test hotels,

DB ops dot js and then we will invoke the API with a get request and the url v1 slash hotels to create

the hotels collection.

Open up the MongoDB Atlas portal in your cluster, hit the button collections, select the travel database

and then click on the Create Collection button.

Provide the collection name as hotels Hit Create and the hotels collection is now created.

The code that I'll walk you through in this lecture is available in the branch.

Partial response.

Let's use the command Git checkout Partial response to switch to the partial response Branch assumption

here is that you have already set up the repository locally.

If you face any DB connection related issue, please make sure that the DB underscore variable is set

with appropriate values.

Let's switch the branch git checkout partial response and now we are in the partial response branch.

Next thing we need to do is make sure that the DB parameters are correctly set up in the cloud.

DB dot JS file.

Once you have set up the DB parameters, the next thing we need to do is add some data to the hotels

collection.

The sample data is available in the hotels dot JS file under the data subfolder.

So this is the data that we're going to add to the hotels collection.

Now to add the data, we are going to execute the script test hotels, DB ops dot JS And to do that,

just say Node test, slash test hotels, DB ops dot JS and give a parameter insert.

And this has added ten documents to the hotels collection.

Let's refresh.

And as you can see here, the hotels collection now has ten documents.

Implementation of partial response will depend on the platform.

The language that you're using here, I'm using MongoDB.

My implementation is going to use the projection option for the fine function.

And it's a very simple thing to use.

What you do is you provide the list of fields with a value and the value decides whether that field

will be included in the query response or not.

A field value of one means the field will be included, zero excludes the field.

So that's as simple as that.

I would suggest that you read through the documentation for MongoDB on projections.

Let's go through the code for database operation for hotels.

So I have this file called Hotels under DB.

There is a method for inserting the data.

This is what got invoked when we use the test DB ops with Insert and this is where we are using the

projection.

So here our function called select and it is taking the criteria and the options in the options.

I have a field for specifying the projection.

It's called fields.

The way you apply the projection is on the find method, select options dot fields will actions or apply

the projections to the queried data and restrict the fields in the response received from MongoDB.

Let's take a look at the Hotels API implementation under API V1 hotels following the convention.

The name of the resource is hotels version is V1 and in the get method, the very first thing we are

doing is checking if we have received the fields.

The idea here is that if we have not received the fields, then all fields will be sent back.

And the way that happens.

If you just provide an empty Json object for fields, then all fields will be sent back.

Here I'm calling a utility method called Create Fields.

And what this method is doing is it's simply taking the comma separated list of fields and creating

a valid Json object out of it in this format.

Next.

I'm creating options object in which I'm simply saying fields column fields and in the DB dot select

method that I walked you through earlier.

I'm passing this options object.

Next, we need to include it in our server, so open index.js.

Here we add this line that says require api v1 hotels and you're passing the router to it so that the

route for the hotels resource gets added to it.

And now at this point we are ready to test.

So let's open the integrated terminal node index.

Let's go ahead and hit the API.

As you can see, I received all the data for the Hotels API.

Now we are going to provide some field names.

Let's just get name and here we get only the name.

Let's get City.

So now we have city and name would suggest that you go ahead, try this function out yourself, make

some changes, play around with the code and share your feedback.