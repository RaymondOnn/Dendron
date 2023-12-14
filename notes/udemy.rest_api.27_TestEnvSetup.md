---
id: rnpp6qyoj7asedeoiqjr8dh
title: 27_TestEnvSetup
desc: ''
updated: 1699966807651
created: 1699965084955
---
Test environment setup.

In this lecture, I'll show you the steps that you need to carry out for setting up the local test environment.

We will carry out four steps in this lecture.

The first one will be to create the travel MongoDB instance.

Second one will be to clone the repository with the demo code, open the code in Visual Studio code

and then set up the DB connection parameters in the code.

After that, we'll execute the test code for populating the vacations collection in the travel database.

So let's go ahead and set up the DB instance.

By default, the MongoDB cloud does not allow network connectivity, so in order to enable network connectivity,

you need to whitelist your IP address and it will be done in two steps.

In step one, you need to get your IP address, go to Google and say my IPv4 address.

It will pop up some of these sites.

You can use any of these sites to get your IP address and here is my IP address that I'm going to copy.

I have my IP address copied in the clipboard.

In the next step, you need to whitelist your IP address on MongoDB Cloud.

Open up your cloud interface for MongoDB Select Network Access and then click on Add IP address, paste

your IP address in the white list entry.

Keep in mind that anyone on the internet can access your DB if they come to know about your user ID

password and your IP address, you may select this button here and this will automatically remove the

whitelisting after the specified time.

For example, six hours, one day, one week.

So I'll leave it at six hours.

Please keep in mind that this setup will expire my IP address in six hours.

And what that means is that after a while my connectivity over the network will stop working and I'll

start to see network errors.

If that happens to you, come back here and add back your IP address.

Now, if you don't want to deal with this hassle, then don't check this box.

Just be aware that your MongoDB instance on the cloud is not secure.

Go to the MongoDB cloud in your browser, click on collections, add my own data, name the DB as Acme

Travel and the first collection will be vacations and hit the create button.

Within this vacations collection, we will have documents with the information about vacation packages

and each vacation package will have a name.

So what we are going to do next is define an index.

So click on index, create index and then give the field name as name.

This is the name of the vacation package and chain this type to one.

For more information about MongoDB indexes, please refer to the MongoDB documentation here.

We also need to add unique crew.

This is the constraint to make sure that package names are unique within the collection.

Hit, create and confirm.

And now our index is ready.

Next we need to clone the demo code repository.

To do that, go to GitHub.com slash Cloud fan, click on repositories, Select the rest API course Dash

v2, click on Clone or download Copy the link in a terminal window type Git clone and paste the link.

Open the source code folder in Visual Studio code and here are the code source files that we are going

to use as our next step.

Open a terminal window and execute NPM install.

This is going to pull the NPM packages and install them locally.

So at this point we are all set with our test code set up before proceeding forward.

Let me give you a quick overview of the various folders in this project.

The API folder will have the source code for the implementation of the APIs.

The data folder has the files that contain the test data which we are going to use from our samples.

The DB sub folder has files that have code for connecting to the remote database for carrying out the

database operations model.

Sub folder has the schema definitions in the JavaScript files.

Test folder has the JavaScript code for populating the collections with documents, and these documents

are the test data which we are going to use for our testing.

Then there is an index.js file which you will use for launching your API listeners.

The sample NodeJS code needs the cluster connection URL string to connect with the travel database.

The format of the cluster connection URL string is this.

The first part is.

The protocol, which is a constant MongoDB plus.

Then there is the user ID and the password which are separated by a colon.

Recall that I have set the user to admin and password to admin.

One, two, three.

Then there is a Add character followed by the cluster host name and then there is a slash and then the

DB name, which in our case is Acme Travel.

Apart from that, you will also see some additional optional parameters at the end of this connection

string.

You can find more information about these additional parameters by referring to the MongoDB Atlas documentation.

Once we have set up the connections in the sample code, we are going to execute code for validating

the connection and for adding some sample documents to the vacations collection.

And to do that we will execute node test slash test DB ops dot JS To get the connection string, click

on Connect Select, Connect your application and then copy this.

Copies the connection string to the clipboard.

Next, go to Visual Studio code and open the file under DB folder Cloud DB dot JS and paste the connection

string.

Now from here you need to pick up the cluster address.

So select the cluster address, copy it to the clipboard and paste the cluster address as a value for

the cluster underscore host variable.

Now check out the DB name.

It is Acme Travel that has been created by us.

And then there is the username and the password.

If you have used a different username and password other than admin and admin.

123 then you need to change those two also.

Now what is happening here is that there is a variable that is getting initialized.

This variable is db underscore Uri and if you look at the format of the connection string here, it

is the same as the connection string that you have copied from the MongoDB cloud.

Now we are ready to test the connectivity.

So let's take a look at the test.

DB ops dot JS file under the test sub folder.

Here you will see that the environment variable db underscore Uri is being set to the db underscore

uri that we have set up in the cloud DB dot js When we will run this code it will populate the vacations

collection with the data that is picked up from the data subfolder vacations.

So this is the data that will be used for populating the database.

Let's go ahead and execute it.

Node test, test DB ops dot JS The code executed successfully as an next step.

We are going to check if the documents have been created in the collections.

So go to collections, click on vacation and here are the documents that have been added to the vacations

collection.

Time to wrap up in this lecture I showed you the steps that you need to carry out for setting up the

testing environment.

We have set up sample code on your machine.

We have set up the database on the MongoDB Atlas and we have populated the vacations collection with

some data.

At this point, I suggest that you take a look at the various folders in the sample code, take a look

at some of the code in the JavaScript files.

Familiarize yourself with MongoDB query and other functions.

Next lecture.

I'll walk you through some of the sample code files.