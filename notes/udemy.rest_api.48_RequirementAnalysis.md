---
id: iqd9wuzfxjom1lg535y1w66
title: 48_RequirementAnalysis
desc: ''
updated: 1699967556911
created: 1699965726163
---
Rest API specifications, process and standards.

In this lecture, I'll cover the process for creating the rest API specification.

I'll also cover the multiple competing standards for the rest API specifications.

In the beginning of this course I define an API programming interface as a user interface to data and

systems that is consumed by applications rather than humans.

Another point I made at that point was that it's a well-defined contract between a provider and the

consumer.

The rest API specification is a embodiment of this contract between the API provider and the consumer.

The developer of the API can create the rest API specification by following the contract Last approach

or by following the contract.

First approach in the contract last approach the developer first creates the code annotates.

It puts a lot of comments in it and then a tool is run on that code that outputs the specification document

and some other documentation in the contract first approach.

As the name indicates, the developer first creates the specification.

A tool is run on the specification to generate the code, which is just a code skeleton, not necessarily

the complete code of the API.

Obviously the developer will have to add on to that code to create the logic for the API and the tool

can also generate some documentation.

The big challenge with the contract last approach is that it is tough to keep the code and specification

and documentation in sync.

It is recommended that you take the contract first approach for Rest API design and development.

The ideal process for the generation of the API specification involves the collaboration between the

app developer and the developer.

Like in any software project, they get together to carry out the requirement gathering, carry out

the analysis, then create the first version of the specifications.

At this point, a mock rest service should be created and this mock is created from the specification

version one.

The app developer should use this mock to validate the function that they are trying to create in their

application.

If it works, then that version is the version that they would use for the real API creation.

Otherwise this iterative process continues till the specification are created, which is agreeable to

both the app developer and the developer.

This final version of the specifications are then taken up by the developer to create the real API.

There are multiple competing rest API specification standards.

Vattel, which stands for web API Description.

Language is an XML based standard similar to Wsdl.

Then there is a Blueprint API Raml, which is supported by MuleSoft and Swagger.

There are other standards which are not covered here.

Swagger at this point seemed to be becoming the standard for rest API specification across the industry.

There are multiple open source projects for API that have adopted Swagger as the standard for their

tooling.

Also, there are API management platform vendors who are supporting it.

Almost all major platforms for API management today.

Support Swagger.

Also, Swagger has a very vibrant community.

You can check out the GitHub.com slash Swagger API to see all the active projects and the work on the

roadmap for Swagger.

Once the developer has created the swagger specification, it can be run through a tool to generate

the developer documentation which can be put on a website.

The app developers can browse search through these APIs to select the API that would work the best for

them in their application.

The swagger specifications can be run through tools to generate the proxies that can be hosted on the

API management platform.

I'll cover the proxies in more details in the API management section.

The same swagger specification can be run through tools such as Codegen that can generate the client

code for the app developer.

So if the app developer is writing Java code, the client will be generated for Java.

Multiple languages are supported by the Codegen tool for swagger.

Also, the same specification can be used for service, virtualization or mocking of the API.

In this lecture I talked about the rest API specifications.

I suggested that you use the contract first approach and a collaborative process for creating the rest

API specifications.

Swagger has become a de facto standard, which most of the vendors and the open source community has

adopted.

There are multiple tools available for swagger.

So once you have created the specification, you can use these tools to generate the developer documentation

to create proxies.

To generate the client code and to create mocks.

The rest of the lectures in this section will go through the details of the swagger specification and

how you can use the swagger specifications for your rest APIs.