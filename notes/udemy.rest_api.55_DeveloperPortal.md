---
id: c56e0tecvg7vntdsf4cdlo9
title: 55_DeveloperPortal
desc: ''
updated: 1699967760995
created: 1699965924208
---
API management developer portal.

As part of this lecture, I'll share some best practices and guidelines around the developer portal

for the APIs.

At the end of this lecture, you should be in a position to decide what features of developer portal

make sense for your APIs.

You will be able to decide whether to build an API developer portal or by a developer portal.

As part of this lecture, I'll also walk you through two demos, one for Apigee and another for MuleSoft.

I'd like to think of the developer portal as a one stop shop for the application developers.

The application developers can go to the portal for referring to the documentation, trying out the

APIs, initiating a request to get access to the APIs and to get any support they may need.

API documentation.

In my discussion on swagger specification, I showed you how you could document your APIs in the specifications

document.

Apart from the information that's in the specification document, there is additional information that

your developers will need.

For example, if you have to show a complex flow on how the API should be invoked, then that cannot

be contained within the specification document.

For that, you will create a separate document and publish it on a web page and then link it with the

specifications using the external docs element in the swagger specification.

This is a good practice.

It is common to see tried feature on the dev portals for a lot of APIs that I work on and this feature

allows the developer to try out the API on the developer portal itself without needing to write the

code.

This is a great feature because it helps the developer understand and how the API works to make things

simple for the application developer and to make them more productive.

It is suggested that you include.

Sample code and sample data on the dev portal.

Consider a scenario in which you have created an API that requires the app developer to populate certain

Http headers with some information.

Now, there are two ways to explain the requirements to the app developer.

One is the traditional way, which is by way of documentation and some illustration, and the second

is by way of providing an SDK.

The SDK will help the app developer get up to speed quickly without needing to go through the complex

documentation.

This aspect needs to be considered on a case by case basis.

I'm on the Walmart Open API developer portal, so very simple.

Portal provides all the documentation, not in the swagger format.

There are some example queries and also they're describing the parameters and then they're giving some

sample responses as well.

The app developer can also try out the APIs.

Now this is more swagger like documentation.

There are a couple of APIs here that you can try out.

For example, if I was to use the search API, I can simply provide the item here and try out.

And as you can see, I've received some data here.

So as a developer it's very easy for me to just try out the APIs and see what works for me.

This is the Uber dev portal.

You would find that on this.

They not only provide the API documentation and the tryout feature, but they also have SDKs.

For example, write request.

You can use the SDK to add a button to your mobile applications.

Before the app developer can access the APIs they need to raise a request for access to the API provider.

The provider or the API owner grants the access or deny the request.

There are two ways in which this process can work.

One is the ad hoc or the manual provisioning process in which there is no automation and traditional

emails or phone calls are made to get the access to the API.

And the other one is the automated process or the self provisioning process which is implemented on

the dev portals.

I'll first go through the manual provisioning process.

The app developer connects with the API owner and requests for the API access by way of email.

A phone call or by way of some kind of an internal ticketing system.

The owner checks the policy for that API.

If it is okay to grant the access, they generate the key and the secret and share it with the app developer.

This process may work in a small team for internal APIs in a controlled environment, so maybe it's

okay to use it for internal teams, but it is not suggested that you use this kind of a process for

public or partner APIs.

In the Self-provisioning process, the app developer logs on to the dev portal, looks up the APIs,

try out the APIs and if they are interested in any specific API, there is a request for access to the

API.

The dev portal checks if the requested API is set up for auto approval.

If it is set up for auto approval, the portal generates the key and the secret.

And the key and the secret are shared with the app developer on the portal.

If the API was set up for, let's say, manual workflow for approval, in that case, the API owner

is notified about the request raised by the app developer.

The owner checks the API policy.

If everything is good, the access is granted by generating the key and secret on the portal, and the

key and secret are shared with the app developer on the portal itself.

Whenever possible, try to use the self-provisioning with auto approval.

Let's go over some of the practices around provisioning.

You must clearly define the role that will be responsible for authorization of access to the API, whether

it is by way of a manual process or by way of self-service.

You would need someone who can take a decision on whether to authorize the access or not.

Set up the access policies for all of your APIs.

So when you are defining the APIs, think about who can access it and set up the policy accordingly.

There may be APIs that are meant only for private use or partner use.

So if there is an access request from a public developer, you may want to deny it and this should be

clearly specified on a per app basis.

Define a criteria for automatic versus manual provisioning.

So in the self-service provisioning model, you can mark the API as automatic or manual provisioning

API.

The idea there is that you have to set up some criteria and there are quite a few factors that can decide

whether the API should be automatic or manual.

Some examples of those criterias are sensitivity of data, highly sensitive data.

You would want to make sure that every API request is looked at before it gets approved, so you will

mark it as manual limited capacity in the back end.

You don't want everyone to start using the API because you may have some constraint in the resources

on the back end.

You may decide to categorize your app developers in some kind of a trust tier system, and then you

can use the trust tier system to decide whether to approve the request or not.

If the API is for internal use only, then it is suggested that you do not publish on the portal.

To avoid these kind of requests coming to you, which you anyway will not grant.

I'm on the Twitter dev portal and I'll demonstrate to you how you can register on the portal for accessing

the Twitter APIs.

So first you have to create a new app.

And once you provide all the details in the app, you will be given the client key and the secret.

So at this point, my application is created.

And now I can use the.

API key and the secret to invoke the rest APIs for Twitter.

So this was an example of the self-service provisioning where the APIs are set up for automatic approval.

The API management platform provide control on the provisioning aspect of the API.

These are just some of the examples of the platform that allow you to set up the APIs for manual or

self provisioning.

Let me show you how it works on Apigee.

On Apigee.

In order to publish an API to a portal, you need to create a product.

I won't go into the details of product.

I'll cover that later.

In the definition of the product, you can decide if the product, which is just a bundle of APIs,

will be visible on the portal or not, and to who it will be visible that is controlled by way of the

access.

It can be internal only.

It can be made visible to users who have been explicitly authorized for viewing the API.

Or it can be public, which is any registered developer can view it.

The key approval is where you set whether the approval will be automatic or manual.

The benefit of using the API management platform is that you can control the visibility and provisioning

aspects of the API by way of configurations.

And the other one is that the runtime and the dev portal are integrated.

As an API provider, you will need to support your APIs.

The dev portal is the one stop shop for all support needs that your application developers will have.

There are multiple factors on which the level of support will depend.

The first one is internal only.

If your APIs are internal only the app developers may be supported by way of internal ticketing system.

You may not need to have a very elaborate dev portal, but if your APIs are public or monetized, then

you have to provide quite a few things on your dev portal to support your app developers.

Here are a couple of ideas you need to provide guidance on how to solve common problems.

So an FAQ.

You need to provide best practices, provide a contact form, provide a bug reporting form where the

app developers can report bugs.

Now, once your APIs have become popular, create a developer community.

Use social media, for example.

Twitter.

You can use Facebook.

Provide a forum on the dev portal itself where the developers can solve each other's problems and discuss

the best practices.

Last but not the least, have the experts from the API provider team write some blogs and publish them

on the dev portal.

Twitter has a thriving developer community.

This is their website for the developer community.

They have this Twitter account where you can subscribe to to get the tweets for the developers.

They have these Twitter developer communities, they have developer forums and they organize these events

that you can attend.

So this is an example of an excellent community which revolve around the APIs for Twitter.

Now the million dollar question, a dev portal is just a web application.

Should you be building it or should you be buying it?

The answer is it depends on your budget and the nature of apps that you are publishing.

Your roadmap.

I'll just share some thoughts with you on the buy versus build decision.

From the build option perspective.

If you are looking for a simple dev portal, which is just informational, then you can simply publish

the documentation generated from swagger.

You don't really need to have a very feature rich dev portal for self-service provisioning though.

You will have to create a workflow or you will have to adopt a manual workflow that I described earlier.

From the buyer option perspective, most vendors are providing their products with the dev portal,

which is integrated with the runtime.

These products also support the look and feel customization so you can change the dev portal to look

like your other websites or customize it to a point that the developers, the app developers will not

know whether they are interacting with the dev portal that was created by you or is a off the shelf

product.

Some of these portals also allow you to extend to include features such as forums, blogs, etcetera.

Next, I'll show you a very short demo on how MuleSoft support customization of Dev Portal.

So this is a short demo, just to give you an idea.

I'm on the MuleSoft management portal and in one of the earlier lectures I had created a API called

Acme Travel using the Raml specification.

Now we are going to add a portal to the.

Acme Travel API.

So here, as you can see, it says create a new portal.

So I'll create a new portal and.

In this, you have a couple of choices.

I can make it public.

I can select a theme.

When I select a theme, I can actually select an image that I want to use for my portal.

So I've changed the image.

Now I can also change the theme.

Here are all the colors that I can change, and now I have my image.

So let's update it.

Next thing is, I need to add the references to the API.

So let's go ahead and add some references.

Let's go ahead and test it.

And here is our dev portal and you can see the APIs here.

So that's how you can customize the portal.

In this lecture I covered the topic of developer portal.

A developer portal enhances the productivity of your application developer.

There are three elements of developer portal.

First one is the documentation of the API along with the tried feature samples.

SDKs.

Next one is the self-service provisioning.

You can automate the provisioning process for your APIs by using a dev portal.

Support.

You need to provide support for your apps, must have the FAQ's best practices and once your APIs are

popular, maybe create a developer community on your dev portal.

Most of the management products come bundled with a developer portal and these developer portals can

be customized and can be extended.

Consider investing in a management solution that provides a dev portal.