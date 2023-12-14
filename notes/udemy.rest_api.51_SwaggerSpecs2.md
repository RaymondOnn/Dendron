---
id: z9k9t7enzd9g1egux30nbfu
title: 51_SwaggerSpecs2
desc: ''
updated: 1699967644813
created: 1699965805731
---
Swagger 2.0 Specification Part two of three.

In this lecture you will learn how to create schemas for your requests and responses.

You will also learn how to create parameters.

Parameters Define how the request data is received by the operation.

Third thing you will learn is the security schemes how to create security schemes that can be applied

to the various operations within your APIs.

The swagger fragments used in the demo for this lecture are available under the Git branch definitions.

There are three types of definitions.

The first type is the definitions.

These are the name schema for defining the request and responses for the API operation in the Path section.

Under operation, references are made to the schemas defined under definition.

Next one is the parameters.

The request data may be received by operations in different ways in the header, in the body query parameters.

So in the parameters you define how the request data is being received.

Operations make references to the parameters under the definitions.

Third one is the security definitions.

There are different type of security schemes that can be used by the operations.

So here you would define the different schemes that are supported by your APIs And under the operations

using the security element, you would make references to the security definition that gets applied

to your operation.

Let's look into the details of definitions now.

Definitions have common schemas that describe the messages for request and responses.

There can be multiple named schemas under the definition.

Each of the name schema have two parts.

The first part is the schema itself, which follows the Json schema standards.

And then there is also some data examples for that schema and you can create multiple such examples

by providing the mime type.

So for example, you can have the data example with XML, you can have data example with Json.

This, these examples are used in the documentation and they are sometimes also used by the mocking

tools.

So when you are mocking your API, the examples defined in the schema are used as responses.

Let's add definitions as a good practice.

Errors are sent back in common format.

So the first thing I've done is I've created the schema name schema called General Error, and there

is only one property in it called message, which is of type string and it's a required field.

So anytime there is any error, the operation will send back the error message in this format.

Next, I'll define the schema for messages in response to something not found.

So, for example, the if the consumer has requested for, let's say, package Bahamas 1000 and that

is not found, then the API operation will respond back with Http status code of 404, along with a

message that would have this structure, the structure.

I'm saying there should be a message and there should be a hint and both are required fields.

Now we're ready to add our vacation schema.

So let's say vacations, very first thing you do is add the properties.

Now the first property we'll add is the package name.

Next one is the description.

The type of the package is enumeration.

So I'll say type.

This is another property.

And here I'll say type is string.

So vacation package type is of type string.

And there are two possible values that it can take resort and cruise.

This is how you create an enumeration in Yaml.

You can also create references in the schema for another schema.

So for example here, let's create a schema called locations location and I'll simply.

There are two properties in this schema.

City and country.

Both are string and both are required.

Now, here in the vacation schema I want to create.

Another property called Destinations.

And these are the destinations for the vacation package.

And this will be an array of locations.

So the type is array and the items are defined like this.

Now, if you notice, the dollar reference is pointing to the location schema.

This is how you create references.

Another thing that we want to put in here is the min number of items and max number of items.

So here we'll say min items is one and max items.

So this is how you create references to other schemas and also define an array and assign a min item

and a max items.

Next, let's add the number of nights in the vacation package.

And as you can see here, the number of nights minimum is one and maximum is 31, and number of nights

is of type number.

At this point, we need to add the required fields.

So let's go ahead and add those required fields.

Out of all these fields, we'll list out as an array what all fields are required.

So we are saying that name, description type and the number of nights are required.

Fields.

At this point, our vacation schema is ready to be used.

There are a number of ways in which the operation may receive the request data or request message.

In parameters, you can define the common ways in which the API will receive the request data or request

message.

There is a name for each of these parameter definitions.

There's a description and the most important part here is the N, because the request data may be sent

in body, in query, in the header, in the path or in the form data.

So these are the types which are available for you to assign to the n element in the parameter definition.

If the request is coming in the body, then there is a need to define the schema as well.

Now you can define the schema as part of the parameter, or you can make a reference to a schema that

is defined under the definition section.

I'll define one parameter and all parameters are under the parameter section and I'll call this one

parameter as ID in path.

In other words, what I'm saying is that the ID for the vacation package will be received in the path

of the URL used by the consumer.

And here I'm saying the name of this is ID, the in is path and the type is string and it is a required

element.

Last one under the definitions is the security definitions.

There are different security schemes that are supported by APIs and swagger.

The type indicates what type of security scheme is applied to the operation.

The name is the one that is used for associating an operation with the security scheme defined in the

security definitions.

The in defines where the security credentials are received, whether it's received in query or in the

header.

The name of the parameter is decided by where the request data is received in.

For example, body.

In this case, no name is needed, but you do need the schema.

Query.

In this case, the name is equal to the name of the query parameter, for example, destination.

In the case of Path, it will be the same as the name of the path element ID.

In this case, in the header it will be the custom header name.

So let's go ahead and define our security definitions.

I'll define one definition which will support the API key based security scheme.

I'll name it key security.

So here we are saying that the scheme is API key.

The query parameter API underscore key will be received in the query.

In this lecture I covered the definitions part of the swagger 2.0 specification document.

There are three types of definitions.

First type is the definitions that has the name schema for the request and responses.

The second type is the parameters that defines how the request data is received by the operation.

And the third type is the security definition that defines the security scheme and how credentials are

received for that security scheme.

The operations defined under the Path section uses the security scheme by way of specifying the name

of the security definition in the security element.