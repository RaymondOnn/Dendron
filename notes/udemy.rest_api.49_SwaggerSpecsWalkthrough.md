---
id: tlf73zk0vwtyb4el11vpy8d
title: 49_SwaggerSpecsWalkthrough
desc: ''
updated: 1699967584353
created: 1699965768153
---
Swagger Specifications.

Introduction.

In this lecture you will learn about the swagger, specifications and Yaml at a high level.

I won't be covering the details of Swagger specifications in this lecture that will be covered in the

next three lectures in this section.

Also, you will learn about the tools available for creation and editing of the Swagger specifications.

In 2010, a company by the name of Wordnik created Swagger specifications for their own internal use.

Later, they open source the specifications in 2015.

Openapi Initiative, which is part of the Linux Foundation, took over the specifications with the goal

to promote it as a vendor neutral format for API specifications.

Today, Swagger specifications are also known as the Open API specification.

The current version is version 2.0 and the specifications can be written in Json and Yaml format.

Yaml is a superset of Json, although you can mix Json and Yaml in the same swagger specification document.

It is not suggested that you do that.

I prefer to write my API specifications in Yaml, and the big reason why I like to do that is because

the specifications become very readable and understandable.

And again the basic reason is that Yaml is meant for human consumption.

It is a serialization language like XML and Json, but it is not going to replace XML or Json.

Mostly Yaml is used for writing the configuration files and it's a superset of Json.

It uses indentation instead of symbols such as greater than less than or braces.

Here is a swagger specification written in Json and this one is in Yaml.

As you can see the Yaml specifications are much more clearer and readable compared to the Json specification.

My suggestion is that you use Yaml for all your swagger specifications.

You don't have to be an expert to write the swagger specification in Yaml.

You can learn Yaml by just using the swagger specification editor and after going through the lectures

in this section I believe that you will be very comfortable in writing Yaml specifications.

There are three editor tool options for creating Yaml based swagger specifications.

The first one is you can use any Yaml editor tool if you are using Eclipse.

There is a plugin for Eclipse called Edit that you can deploy on your eclipse based IDE and use it there.

Second one is the online swagger specification editor.

Let me show you how that works.

The online Swagger specification editor is available at editor swagger.io.

You will see the first API specification that it opens up as an example is for Uber.

The left hand panel is for editing and the right hand panel shows the API documentation that is getting

generated in real time.

From your specifications, you can open other examples by using the file open and then if you already

have a file that you have created in an earlier session, you can import it or you can simply cut and

paste it here.

You can download the specifications you created in the Yaml or Json format.

Another good thing that you can do from this editor is you can generate the server code.

So once you have the API specification ready, you can generate the code for writing the server and

there are quite a few options available here.

You can also generate the client for your API.

And then again there are quite a few options here.

I suggest that you go through the examples and look through the documentation and play with this editor.

It's a pretty cool editor to play with.

The third option is to install the Swagger tools, which are node tools available from NPM.

You can deploy the same online editor locally by installing the Swagger editor package.

Another tool which is available is the Swagger Tool.

It's a collection of tools, not just the editor, but it provides you a framework to create swagger

based APIs.

Let me show you what all you get with the Swagger tool after you have installed the Swagger package,

you can use the command line option for creating a swagger project.

In the Swagger Project, you launch the edit option that shows you the same editor as the online editor.

There you provide the Yaml specifications for the swagger, and while you are doing that, you can also

try out the API which is getting built in the background based on the specifications and it shows you

some mock data.

Once you are done with the specifications and you have tested the mock APIs, you are ready to code

the API after the code is done.

After the testing is done, you can publish the code generated on any NodeJS platform.

In the next three lectures in this section, you will learn how to create Swagger 2.0 specification.

As an example, I'll create the Swagger specification for the Acme Travel Vacations API.