---
id: mvrn4uyhhaacjy2bhuur6ur
title: 34_WalkthroughAPIVersioning
desc: ''
updated: 1699967078154
created: 1699965316694
---
Rest API version lifecycle.

In this lecture I'll cover the Acme Vacation API version strategy and I'll also share some tips on how

to manage multiple API versions.

So now it's time to make a call on how you would manage versions for Acme API.

The one thing is for sure that Acme will follow the API versioning for all its APIs.

The standard that we will be using is V, followed by the major version only in the URL.

So we will not be using the minor version.

We will not be using the header or the query parameters for managing the version.

Here is an example for a set of vacation packages.

The V1 here is the version.

Another example is for creating a new review in this post API.

V2 is the version.

Next I'll discuss a common API chain strategy based on Versioning.

Consider an organization that has a quarterly release cycle.

In other words, new versions of the apps are released on a quarterly basis every three months, and

this organization decides to follow the same practice for their apps.

So in Q1, the release an API version one of the API, and in Q2 they made some changes to the API.

But instead of making in-place changes to version one, they released version two of the API in Q2.

When the version two is released, they will mark the version one of the API as deprecated.

What that means is that it's no more available for subscription by new developers.

The existing applications will continue to use version one.

There will be no impact on that.

Newer subscribers will be given the access to version two only.

They will not be able to subscribe to version one.

And then in quarter three, the deprecated version one will be retired.

So in effect, what you are telling the existing application developers or owners is that version one

will be retired in Q3, so they should plan to upgrade the applications to version two of APM.

Then in Q3, version three of the API will be created and made available.

And obviously at that point, version two will be marked as deprecated in Q2.

Both version one and version two are available in Q3, Version two and version three are available in

effect at any point in time.

There are two versions available to the developers.

The idea here is to assist the developers in planning in transition by supporting multiple versions

at any point in time.

Let's take a look at the key points related to the multiple version support.

First is support at least one previous version for a period of time.

The idea here is that this would give enough time for the application developers to make changes to

the app for accessing the new version of the API.

Now, how much time should you be giving?

That depends on your organization.

Typical I have seen is three months.

Please look at your specific situation in terms of the change processes you have in place, in terms

of the processes you have in place, and then decide whether for your organization it will be three

months or six months or maybe one month Mark the previous version as deprecated.

So when you release version two mark, version one as deprecated, and at that point, once the version

one is deprecated, no developer can request for access to that version.

That is the deprecated API.

Any new request that comes for access will receive access only to the latest version.

Publish a rollout plan in advance.

Again, help your application developers in planning and transition manage change log that clearly shows

the reason for new version.

The idea here is that the developer should see the value in moving to the new version.

Also, they should understand what all has changed so that they can take advantage of new features or

handle any bug that may have been there in the previous version.

As the API provider organization, you need to develop your own practices for managing API version lifecycle

to provide the best experience to your app developers.

You should create a roadmap for your application developers.

Each API version has to go through certain phases in lifecycle it's made available, then it gets deprecated

and then it is retired, providing a clear cut roadmap in terms of when the API version will be available,

when it will be deprecated and when it will be retired, will help the app developers better plan the

releases of their own applications.