---
id: 4y7v9uf69g3spqk0q9ydmyg
title: 52_SwaggerSpecs3
desc: ''
updated: 1699967672065
created: 1699965829027
---
So I got to point out, specification, this is the last lecture in the three part series on Tiger 2.0

specification, at the end of this lecture, you will know how to create parts in the specifications.

You will also know how to use definitions in the math parameters, responses and security.

At the end of this lecture, I'll show you the demo of Developer Portal with the Vacations API, these

Saigo fragments used in this.

Facts are available on GitHub under the.

That's fine, all your resource parts in the ad section of the specification document, there can be

multiple pad definitions within the specification document.

For example, you can have slash vacations back and slash portals back in the same specification document.

Each pad can have one or more operational.

The operations definition under the act began with a valid SCDP method such as that proposed, etc.,

The summary and the description for the operation provides a short and a long description.

The tags refer to the predefined tags which were created in the first part, or it may be new tax.

My suggestion is to always predefine the tags.

External Documentation Link is the link to external documentation specific to this operation.

So this has nothing to do with the external documentation link that was created and the specification

level.

Although you can define the parts anywhere in the specification document, it is suggested that you

define the parts before the definitions and that's a convention that people usually follow.

So let's go ahead and define the parts.

I will be creating the parts and operation for specific vacation package.

So this is our part.

And the first operation is get and this is where we will put the summary and description.

So this is how our pet looks like for a specific vacation package.

Get operation.

The next two elements are consumers and producers, they decide the mind types that are consumed by

the operations and the mind type produced by the operations, the consumes and produces uses an array

of mine types, such as application, JSON, application, XML, plaintext, whatever mind type you

want to use for your operation.

Let's go ahead and add the mind types for our API.

Since it's an area we will use the dash.

Next one is the parameters, the parameters object besides how the request data will be received by

the operation.

Yet there are two ways in which you can define the parameters.

Either you can refer to the parameter objects defined under the definitions that we did in the last

lecture.

Or you can create the parameters right under the operations by providing all the elements in the demo.

I'll show you how I would use the parameter that was already defined under the definition.

Let's take a quick look at the parameter that we define under the definitions.

As you can see here, we have defined one parameter in part, so what we are saying here is that the

parameter is received in the path of the euro and it is a required type.

It's in the path and the name is it.

Let's go ahead and create the parameter known.

There can be multiple parameters, and in this particular scenario, there is only one parameter.

Next one is the responses for each SCDP status code that your API operation will send back.

We would need to provide a response so there can be multiple responses for an operation one, but it

should be status code.

You can use the schemas that are defined under the definitions for the responses, or you can create

a schema under the operation for the responses.

So responses always start with the responses, then you give the status quo.

So for the success, it has two hundred and this is where we will define our responses, the description

and the schema.

So here I'm saying that in the case of success, the status quo will be two hundred and the data will

follow the vacation schema, which is already defined under the definitions.

Next, let's add another response, code four or four, which is if the requester asks for a specific

vacation package and the idea was not found.

Now, this is, again, a reference to not phone data that we had created.

There's also an interesting one, which is the.

So for all of the status quo, we'll send back a general letter.

And the general letter was also defined in the definitions.

So these are our responses for the get operation.

The last one is the security, which refers to the security scheme defined under the security definitions,

I'll be using the API key security definition created in the last lecture for the double.

I'll be using the security definition, cue security for the get operation, so let's go ahead and add

the security.

Here you see this area.

This is for all, not only for the basic authentication and security, you don't have to provide any

elements in this area, but for all, you have to provide the scoops.

So here you go to the empty empty-headed.

And now we have added the APK security to get operation.

The final version of the vacations appears like a specification document is available on GitHub under

the get branch final.

I'll be using the final document to show you how it looks on the developable.

I'm on the online editor for Swagga and I'm going to just paste our API definition here and now.

This is the documentation that's generated for the specification that we just created, as you can see,

we have the security.

Which is defined as a key security that requires the key, then we define three tags, vacations, hotels

and package tags appearing over here.

We can filter based on these three tags.

So I say it basically filters the apps that have a tag to find locations so mean.

In this case, I just have to have parts defined vacations and vacation ID.

So it may not appear to be as useful, but in case of a specification document that has multiple parts,

this can be very useful here.

As you can see, the vacation ID, we have our response codes to hundreds and the vacation schema that

was associated with the response.

Same way we have the photo for associated with the not found error and then default general error.

Also, you would see that the security here is associated with the security.

The models that were defined under the definitions will appear under the model section.

So as you can see, without putting in any effort with the specifications, we could generate a beautiful

looking document for our app developers.