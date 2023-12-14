---
id: hr3ewxj2wh9p2o7koup5rra
title: 50_SwaggerSpecs1
desc: ''
updated: 1699967613564
created: 1699965787034
---
Swagger 2.0 Specification Part one of three.

At the end of this lecture, you should be able to explain the structure of the Swagger 2.0 specification

document.

You should also be able to explain how to add metadata to the specification and what elements in the

specification define the URL endpoints.

You will also know about the tags and the external links that can be added to the specification to make

it more helpful for the application developers.

The Swagger 2.0 specification document has three parts.

The first part has the global information that applies to the overall specifications.

It has the metadata, the external documentation links the tags which make the specification developer

friendly by making it searchable.

The second part has the definitions, which are the common schemas for request responses, the parameters

and the security that can be applied to various resource operations.

The third part is the most important.

It has the resource definitions.

It makes references to the previous two parts.

So for example, the request schema and the response schema are references to the schemas defined under

the definitions and the tags under each of the operations in may be references to the tags that is defined

in the Part one.

In this lecture I'll cover the part one, which is the metadata route document.

External documentation and tags.

The swagger document I'm using for this demo is available on GitHub.

Please clone it and to see the fragments I have used in this demo branch to route document.

The swagger metadata is in the information object.

The information object has three properties version, title and description, which are self-explanatory.

It also has two other objects.

Contact information.

This is the name of the contact person for this specification and the license license.

Again a name and a URL.

The contact and the license are optional.

Let's go ahead and create the swagger metadata now for our vacations API.

The Swagger 2.0 specification document always begin with this line which indicates that it is version

2.0 of swagger.

The metadata is contained in the info object, so we create the info object and here we have the version

as 0.0 0.1.

There is a title which is Acme Vacations API and then there is a multi-line description to create multi-line

description you can use the pipe character.

Next, I'll add the contact object within the info.

Pay attention to the indentation here.

Info contains contact and here I'm specifying the name as a cloud fan.

I have the URL and the email for myself.

Next object is the license.

And the license has name and URL.

So that's our metadata for the specification.

The root document has properties that are used for creating the endpoint URLs.

It has the attribute called host, which is in the format of hostname colon port number.

It has the information on bass path.

Now bass path is the path that is prepended to the resource name.

Then it has the schemes in production.

You would not use Http, but in development you can have Http and Https.

So these are the supported schemes.

The path is created by concatenating the resource name with these three strings.

So for example, for vacations it would be Https or http colon API dot acme.com colon 3000 slash v1

slash vacations.

The last two attributes are for the content type header that indicates the type of the response produced

or the type of the data accepted in the request.

Since we'll be testing locally, I'm going to create the host as localhost colon 3000.

Next one is the base path which we decided to have as V one because we are creating the version one.

Next will be the schemes schemes.

We are saying because we are in development we can support both Http and Https.

Now note how I'm creating the arrays.

This is the way Yaml defines the arrays.

You have dashed to indicate each item for producer and consumer.

I'm using application Json, so that is our root document.

The swagger specifications documents are used for multiple purposes.

One of the purpose is to generate the documentation for the app developer.

The tags elements are used by the developer portal or the tools for developer portals to generate the

filterable and searchable documentation for the APIs.

Tags is an array that contains multiple name description pairs.

The tags are referred from the paths and the operations section, which is the third section in which

we will define the resource.

So as a good practice, always use tags in your paths and operations because it will make your APIs

searchable and filterable on the developer portal.

Let's go ahead and define a couple of tags.

So you see there are three tags that I have copied here.

The first one is the vacations.

There is a description.

Next one is the hotels description.

And then the third one is the package.

The specification document cannot hold all the information or all the documentation for the API, and

you want to make sure that your app developers are getting as much help as possible.

So that's where the external docs comes into picture.

The external docs object can be defined under the root document.

It has two properties, description and URL.

As the name indicates, it points to the external document repository of some kind.

So for example, let's say you have an FAQ for your API.

Obviously you cannot build that documentation in the specs, so what you can do is create a web page

and put that there and then use that link in the external docs object.

The external docs object can also be created for paths.

As a good practice, I suggest that you create a website for your APIs and link it with your specs using

the external docs.

Let's add the external docs.

So I've added the external docs object in the root document for travel.

And as you can see here, I have the description and the URL.

Swagger 2.0 Specification document has three parts in this lecture I covered Part one.

In part one there is metadata which is defined in the information object that also contains the contact

and the license information.

The root document has scheme host base path, and these three elements are used for creating the endpoint

URL.

ERGs defined in the specification are used by the developer portal tools.

The developer portal makes your specification searchable and filterable By way of using these tags,

you can define multiple tags in the specification and you make references to the tags in the path section.

Since all information cannot be added to the specification, there is an option to create a external

website and provide a link to it by using external doc object.