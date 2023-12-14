---
id: kcnjyb9oqhcqfkoyy43la2m
title: 54_LifecycleAndDeveloperProductivit
desc: ''
updated: 1699967741235
created: 1699965897285
---
API management lifecycle and productivity.

In this lecture I'll cover the lifecycle from the API management perspective.

Also, I'll cover certain considerations for enhancing the productivity of the developer and the app

developer.

I've included three demos on three different API management products to show you some of the features

available on these platforms to enhance the developers productivity.

There are four stages in the lifecycle of Napier.

The first stage is the build stage.

In this stage the API is created by the developer.

Once the API is tested and validated, it gets into the public stage.

In this state, the app developers can request access to the API.

If the access is granted, the app developers can access or invoke the APIs.

At some point the published version of the API is marked, deprecated and is replaced by a new version.

The deprecated version of the API would not allow the new app developers to request for access.

They will have to go for the new version.

At some point the deprecated API gets retired and at that point the API code may be removed from the

infrastructure in order to support the lifecycle in an organization.

The organization must establish some processes, practices and rules.

For example, when the API is taken from build to the publish stage, does the developer have to provide

some kind of documentation as part of the process?

And who will carry out the publishing of the API?

You must publish a roadmap in advance.

In other words, if you have a plan to create a new version of the API, provide the dates for, publish,

deprecated and retired stage in advance if possible, especially if your app.

App developers are external to the organization.

There's a good practice.

You should keep your application developer community informed about the lifecycle stage transitions

in advance if possible.

If your organization has already invested in tools such as System Net or IBM for governance, you may

use those tools for the API lifecycle management as well.

API lifecycle management feature is not a standard feature on the API management platforms.

I'll take an example of IBM API connect that does support the API lifecycle management in API Connect.

The API starts its life in the draft mode, then it goes to the plan stage and then goes to the stage.

At this point the API is ready to be published.

Once the API is published, it is available for provisioning on the API connect developer portal.

At some point the API can be marked as deprecated by the owner.

This whole process is supported by the API manager for the IBM API Connect.

Next topic is productivity.

We have to look at productivity from the point of view of the app developer and the developer from the

app developer's perspective.

The app developer would like to see some great documentation for your API.

They would like to see some samples, examples and SDKs.

So API documentation is a must.

Self-serve provisioning is basically related to how the app developer will get access to your APIs.

Do they send an email or make a call?

Not a good idea?

And that is the reason you have to think about some process of self provisioning, some kind of a website

or something that you can use for letting the app developers request for access.

Third one is the support.

If the app developer has a problem of some some sort, what do they do?

Where do they go to and get the help from?

The answer to these questions is a developer portal.

You need to come up with a developer portal that the app developers can use for getting help, for getting

the access to the documentation, for initiating the access process.

Looking at it from a developer's perspective, the developer should be able to make quick decisions

in regard to how to implement the APIs.

What are the best design options and other aspects related to the implementation of the API?

So as a technology lead for the API, you must think about the development guidelines, the frameworks

for creating the APIs and some best practices that the developers can leverage.

All management solutions that I have experience with support the design and development of APIs by way

of frameworks, by way of features.

One of the common features I have seen in most of these platforms is related to the import of specifications

for the creation of APIs.

For example, you can import a swagger specification in API, connect or in the API and you can import

Raml based specification on the MuleSoft platform.

And in each of these cases, the API gets created just by the import of the specifications.

Another thing that you will see with the API management platform support is they provide a number of

utilities and tools to enhance the developer productivity.

Let's take a look at a couple of these tools and features.

Signing on to my MuleSoft account.

I'm going to the manager to create a new API.

To create a new API.

I need the Yaml, which I've already created, so I'm just going to create new.

API.

And in the designer I can just paste the grammar.

And once paced the ramble.

As you can see, I have the vacation which is ready.

Now this API is ready to be invoked because I already have the base set up to route the calls for the

proxy to the back end.

So this is how simple it is to create the API with specification on MuleSoft.

Now let's take a look at another platform.

IBM API Connect product allows you to import swagger specifications or open API specifications.

So let's go ahead and do that.

Import an existing open API, which is nothing but swagger.

I'll select the swagger file that we created for vacations.

As you can see, this is the Swagger specification which I created in the last section during the discussion

on Swagger Specifications.

All the information that we had put in there is now available here.

My suggestion to you would be to try this out on your own to get a feel of how API management products

work with the specifications apart from coding the functional requirement of the API.

The developer also has to build the non-functional requirements such as the security and traffic management

coding.

The non-functional requirement in the code will make it very complex and over a period of time these

non-functional requirements may change and every time there is a need for a change, the code will need

to change.

Most platforms have solved this problem in a very elegant way.

They support what is known as policy based implementation.

So what they suggest is that instead of building all these non-functional requirements in the code,

you apply policies which are written in a XML or Json format.

Think of these policies as configurations.

Anytime there is a change in the requirement, you can change the policy.

So for example, you are using basic authentication for your API and now you want to switch it to,

let's say, OAuth.

In that case, we are talking about switching a policy, not necessarily making a code change.

Some platforms use their own proprietary policy markup language.

For example, MuleSoft has what is what they call MuleSoft expression language.

There may be situations where policies may not be sufficient for building the function that you are

looking for in the API.

In that scenario, most platforms also support languages such as JavaScript, Java and Groovy.

In fact, there are other languages, but these are the common languages I have seen being supported

by the API management platform.

Now let's take a look at the policies.

For the policy demo, I'll use the Apigee management platform.

I'll use the target end point as the Google Geocoding API.

And in the demo I'll first create a proxy for the geocoding API which will be available on the URL.

Cloud fan dash test dot net slash geolocation and then I'll add a policy to it to carry out basic authentication.

I'm on the RPG portal and to add a proxy, I'll just click on this plus API here.

I can use the open API specification.

I'll simply go ahead and click on next and here I'll give the proxy name and the target URL.

Target URL, as you can see, is Google APIs next.

And this is a proxy pass through so I won't apply any policy.

And now build and deploy.

It's built and deployed.

Let's see if it is working.

So this is our proxy.

As you can see, it's working, but it says that the address is missing.

So I'm going to provide the address and here is the information from the geolocation API.

And as you can see, I'm actually hitting the proxy, which is sending the request to the Google Geolocation

API and sending the result back as a next step.

I'm going to protect this API proxy with basic authentication.

In order to protect the proxy with basic auth, I'll apply the basic authentication policy.

To do that, I go to the develop tab here and simply say add a step and look up the basic authentication.

Here is our basic basic authentication policy.

Apply it, save it.

And I'm done.

No coding required.

Go back to my.

The geolocation.

And here, as it says, it's unable to.

Call the API because the basic authentication parameters are missing.

So let's give the basic authentication parameters username equal to a cloud fan password equal to whatever.

And there it is.

So as you can see, without any coding, just by adding a policy, I could protect my API with the basic

authentication.

Obviously I haven't implemented the the user store for the basic authentication, which would require

some configuration in the policy.

API uses XML for policies.

The basic authentication policy that we added generated this XML.

The developer can change the XML in order to change the behavior of the policy.

For example, here in this policy, the username is received in the query parameter.

If the developer wants the username to be received in the header, they can simply change it to point

to the header in which they would like to receive the username.

In this lecture I covered the lifecycle and developer productivity API lifecycle has four stages build,

publish, deprecated and retired.

Not all API management tools support the lifecycle management aspect, so when you are ready to select

an API management product, you need to think about how important lifecycle management is for you.

From the API management solution perspective, it is important that you set up the processes, practices

and roles for managing the lifecycle in order to help your API developers become productive, you must

consider creating the guidelines, practices and frameworks for the implementation of APIs.

The API management tools provide policy based implementation.

That is, there is no coding required or minimal coding required.

Consider those tools as they will help your API developers become very productive.

The application developers will need access to the documentation.

They will need a way to create a request for access to the APIs.

Also, they will need support in case they have issues.

The suggestion is to consider building a developer portal on which all these aspects may be covered.

I'll be discussing the developer portal in details in next lecture.