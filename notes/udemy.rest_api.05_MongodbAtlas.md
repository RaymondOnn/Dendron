---
id: afrktq60duvjsqs9lrblptv
title: 05_MongodbAtlas
desc: ''
updated: 1699966192259
created: 1699964361249
---
You will be using an instance of the MongoDB database.

Earlier, I used to use MLB.com for creating MongoDB instances, but since MLB.com has been acquired

by MongoDB going forward, I'll be using MongoDB Atlas.

Now, if you already have an instance of MongoDB on MLB.com, you may continue to use it.

Otherwise you may create an instance of MongoDB on the MongoDB cloud.

Please note that the course was originally developed using MLB.com.

The difference between MLB.com and Cloud Mongodb.com is the look and feel of the website and the interfaces.

The functionality stays the same for instances hosted in either of these environments.

To create an instance of MongoDB on the MongoDB cloud, hit the URL mongodb.com slash cloud in your

browser and get started free.

Here you will have to create an account.

I already have an account so I will just log in, provide your email ID, provide the password and log

in.

Once you log in, you will see the screen where you can create free instance of the MongoDB cluster.

So click on create and select the free tier.

Now you can create an instance of MongoDB on AWS, Google Cloud or Azure.

I will go with AWS, so I'll simply hit Create cluster.

You will be asked to select some images and verify.

It may take a couple of minutes for MongoDB Cloud to create a cluster for you.

Once the cluster is created, you need to create a database user.

So click on database access under security add new user.

I'm going to call this user admin and password will be admin one, two, three, add user.

And now our cluster is ready for a database instance, hit collections button and then hit the button.

Add my own data, provide a database name, I'll just call it test DB since we are just testing and

then provide a collection name which I'll set to collection one.

Hit the button for creation.

Once the database is created, you will also see the collection and to insert documents in this collection,

hit the button, insert document, provide the Json data for the document name.

Sam.

Let's add a number in 32 age.

Let's say 20.

Insert document.

Once the document is inserted, you can carry out queries.

For example, I can run the query with the criteria name gym.

Since there is no gym in the collection, the query returns zero documents.

If I change this to Sam and click on Find, we get the only document which is there in the collection.

Now in order to delete the database, just hover over the database name, click on the delete icon,

provide the name of the database and hit drop time.

To summarize.

In this lecture, you learn how to create an instance of the MongoDB on the cloud.

We have used the MongoDB Atlas offering that lets you create MongoDB clusters in the public cloud.

At this time, I suggest that you play with the interface provided by MongoDB Atlas as you will be using

it in later lectures.