---
id: exorik5u9t0z0iowb26luh4
title: 31_ErroHandlingForPOST
desc: ''
updated: 1699966908000
created: 1699965207926
---
Dharma Vacations API error handling.

In this walkthrough, I'll show you the code for the Acme API for vacations, for the error response

template, for setting up the specific error codes and handling the DB and validation related errors

for the post operations for the vacations.

Before I proceed with the walkthrough of the error handling code, let me ask a quick question.

What will happen if I insert or add a vacation package with a name that already exists?

Now you know the answer.

It will fail.

But what will be the error message?

Let's check it out by running the code in the original code for the post method on DB save.

If there is an error, then we are sending back a status code of 400 along with the error received from

mongoose in the body.

This is what needs to change.

Now.

I'm going to start this app and try it out in Postman.

I'm going to try to post a vacation package that already exists.

So, for example, here you see that Bahamas 1000 is already there in the collection.

So if I try to send it, it should lead to an error for duplicate keys.

And as expected, we received a 400 bad request and an error code of 11,000.

This is the error that is sent back by API and this is the same error that our code received for MongoDB.

We send this back to our application developer or the consumer is not going to be happy.

Why?

Because this error doesn't really tell a lot about what happened.

It simply says duplicate key error.

Also, what if tomorrow the developer on the vacations side decides to switch to a different database?

For example, Cloudant.

Now this error will look very different and your application consumer will have to change as a result.

Standardizing the template is important.

So now let's go back and see what we have to do.

Okay.

Vacations.

The code that I'll refer to in this lecture is available in the rest API course v2 repository.

And the branch that you have to switch to is error handling.

So my assumption here is that you have already set up this repository on your local drive.

What you need to do next is do a git checkout error handling and it will switch the branch to error

handling.

The other assumption that I'm making is that you have already executed the test slash test db ops dot

js file to add some test data to the vacations collection in case you run into a DB error, please check

db underscore uri variable in the db slash cloud db dot js file.

So in order to implement the error handling for our vacations post operation, I will follow five steps

that I'll walk you through.

The first step is setting up the error codes and description for the specific errors.

Second step will be to create the template for the error response body, and this template is the same

that I have already walked you through in the lecture.

Then we will code the utility function for creating the error responses and implement the error handling

code for the post vacations operation.

And in that operation, we will send back a status code of 400 for any duplication or any other validation

error.

For vacations.

Error codes.

I have decided to use four digit error code.

The code 5000 is a special code for unknown error.

Any error that starts with number six will be a database error such as duplicate keys.

Any error that starts with seven will be for any kind of validation error.

Let's start by checking the branch.

So git branch.

And as you can see, we are in the master branch.

So the next thing we are going to do is switch the branch by executing git checkout error handling.

And at this point our branch is switched to error handling.

You will observe that after you do a branch switching there is this new folder util.

This is where we have some new files that I'm going to walk you through.

Another important point to note here is that under the DB makes sure that the DB connection parameters

are correctly set.

If not, set them up for your database cluster in errors.

Dot js.

There is an object called errors, and this is the object that has all the error codes which are specific

to vacations.

The very first error is an unknown error with the code 5000.

The idea of this error is that if you have not identified a specific error and you have not handled

it in your code, you can use the unknown error, but you should avoid it.

And as you start to see through the logs and you see that there is an unknown error, then you should

enhance your code to take care of those specific errors.

Next error is the package already exists.

So this is the code that will be used or this is the description and error that will be used if the

if the consumer has sent data with a package ID that already exists.

So this is the error message which will go back to the developer vacation package with provided code

already exists and then there is a hint in it.

Please use port for update instead of post because maybe the intent of the developer is to carry out

the update, not insert.

So that's the hint.

And then it has the information a link to the documentation.

Let's go through one more error here.

Next one is the missing package name.

So the name of the package is required field as per the schema we have set up.

So when we will try to insert the payload that does not have a name, Mongoose will throw a validation

error.

So Series 7000, I have started to use it for validation errors.

So here it says required field vacation name is missing.

So this is how I have defined all the error codes at this time.

Now there are roughly seven error codes that I have defined, but as we will start to progress further,

we'll add additional error codes in the errors file.

You'll also find a create method.

This create method is a utility method that simply creates the body of the error in a standard format.

So there is a function that takes message.

This is free text message, an Http method, the endpoint information, the error list and the received

payload.

Again, this is the optional parameter.

And what this method is doing is it is creating Json object with the text message adds a timestamp to

it.

Method is set to Http method sets of the endpoint and then adds the error list.

And that's what the caller gets in response to call to this method.

This method is invoked from the API.

So now what we have to do is change the code in vacations, in the vacations API post method.

As shown earlier, this was the original code and now that code has been replaced with process mongoose

errors, which is returns a user error that has the specific error in the standard template that we

have defined.

And then we are setting the header to application Json and then sending back that error response with

status code set to 400.

Now the key method here is the process mongoose errors.

Let's take a look at that.

The process mongoose error.

What it is doing is it is checking if there is a validation error or if there is a database error.

11,000 that I showed to you earlier.

If it is a validation error, then it basically goes through the list of errors received from Mongoose

and convert each of those validation errors into standard errors that we have defined for our vacations.

So for example, check if there is an issue with the number of nights here.

If the error has errors related to number of nights, then we are checking if the number of nights error

is of type menara, which indicates that the number specified is less than the allowed minimum, or

if it is more than the max which we have set in the schema.

So those Min and max have already been set in the mean in the schema as one and 31.

Then we are also checking if the received value for the number of nights is a non number.

In that case there will be a number error.

So based on what we have received for the number of nights we are sending back or the error details

for format number of nights.

Now this is defined in the errors here.

So this is the error detail which will be sent back to the caller.

Let's go back to vacation.

Same way I'm going through the other validation errors.

For example, is there a problem with the name?

It's a required field and if the required field is missing, we are sending back the details saying

missing package name.

That's the details of the errors we have already defined.

So an array is created with all the validation errors and that array is then set in the error response

body in the error list.

Now, if there is no validation error, there is no 11,000, then it's an unknown error.

So then in that case I'm setting the error to unknown error.

And one other thing you will see is that I'm setting the payload to error and the reason is because

we ourselves will need to know which new errors occurred, which we are not covering in the validation

or error, the database error, and what should we be doing with it?

So this would help us implement new errors that we may have missed out.

So that's the intent behind the unknown error, and that's how you create the error response, which

will go back to the caller.

Let's go ahead and try out the code now.

I'll try to insert the same row that we tried earlier with the same name.

Now, this should lead to a duplicate index error, but this time we will see our error.

So now you can see status 400 with an error that we have defined in our API.

So this is this is good.

Let's try out another error.

This time I'm going to remove the name and I'm going to change the number of nights to, let's say 32,

which is going to give a max error.

Now we should see two errors which are related to validation.

So as expected, we see two errors here.

One is number of nights, must be a number between 1 and 31.

And another one says required field vacation name is missing.