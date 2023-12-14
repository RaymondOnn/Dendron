---
id: 60s5k9l4jyidz8hqn5rkl1m
title: 56_SecurityManagement
desc: ''
updated: 1699967781480
created: 1699965945117
---

API management security.

In this lecture, I'll cover the topic of managing the key and Secret and OAuth 2.0 from the API management

perspective.

As part of my discussion, I'll also walk you through a demo on how you can manage key and secret based

security on the Apigee API management platform.

There is no silver bullet for countering the functional attacks.

They are the most common form of attacks carried out against.

I'll share my thoughts on some best practices for countering the functional attacks, and I'll also

share some general best practices for security.

Let's consider a scenario in which you are creating an API that would serve publicly available data.

Now, what kind of a security would you need for it?

The answer may be that don't need a security because it's publicly available data.

The good practice is to always ask for a minimum of API key as a security measure.

In other words, the client can invoke the API, but they will need to provide an API key.

The idea behind the minimal security with API key is that if there is a misbehaving application then

you can secure your API by invalidating the key after you invalidate the key.

The misbehaving app will not be able to invoke your API.

Another reason is that you will be able to generate the analytics with the key as the identity of the

application.

And the third reason is you can generate usage metrics for that user or the app developer.

As an API provider, you need to manage the keys and the secrets you need to implement the API key and

secret based security in your APIs.

Let's look at the app developer from the Key and secret management perspective.

The app developer is in control of the key and secret.

They may create the key and secret on the developer portal.

They may renew the key and secret on the developer portal and at some point they may even delete the

key and secret.

As an API provider, you need to support this process.

Building a key and secret management system is not a trivial task.

I suggest that you look at your options in terms of the commercially available solutions for key secret

management.

The API platform provide you the key management solution out of the box.

In this demo I'll use the API key storage management features.

I'll show you three things.

First, I will show you how to enable API key based security for the geolocation API.

The geolocation API is the same API proxy that I created in one of my previous lectures.

Second, I'll show you how to set up the API key and secret for the developer and use it for invoking

the API.

Third, we are going to invalidate the key to show that if there is a problem, the key can be invalidated

by the provider and hence the access will be denied for any future calls from that same app developer.

Let me first show you the original proxy behavior.

This is the original proxy behavior where if I provide an address, the zip code, it responds back

by providing me information about that zip code.

Now we're going to make a change to the API.

So geolocation develop and I'm going to just add a step here for key validation.

Verify API key.

Save it.

Now let's test.

Now, when I invoke this, I get a message saying fail to resolve API key variable and this API key

variable is expected in the query.

So I'll just provide the API key.

With the junk value.

And see what happens.

Now.

This time we receive a message saying invalid API key.

And that is because when Apigee tried to find out the API key value in the storage, it did not find

it and that's the reason it's saying it's an invalid key value.

Next, I'll create an API key secret by creating an app on apigee.

And now we have a API key and secret that we can use.

This is the key and this is the secret.

I'm going to just copy this and use it in the API.

I'll replace the junkie with the valid key.

And this time the API worked.

And the reason it worked is because now we are providing it a valid key.

Next, I'm going to delete this key and then see what happens.

To delete the key.

I'm going to simply delete the app.

The app is deleted.

Let's try this again.

And this time again it says it's an invalid API key.

So if there is a misbehaving application from the API portal, you can delete that application and that

application's key will no more be valid.

It is advised that you do not implement OAuth in your code like using passport with NodeJS instead.

As an API provider, you should look into the option of getting a off the shelf authorization server

for OAuth.

This authorization server will need to connect to your enterprise identity and access management solution

such as your Ldap.

Also, there will be a need to manage the storage for the tokens at a high level.

This authorization server will take care of your tokens, your scopes and the clients.

Since 02.0 has become the de facto standard for API security.

Almost all API management platforms provide a OAuth based authorization server out of the box.

Functional attacks are the most common type of attacks which are carried out against APIs.

There is no silver bullet to tackle these kind of attacks.

There are some good practices that you must follow set of testing practices with the idea on creating

scenarios which will mimic the functional attacks.

Adopt some tools to carry out the functional attack on your APIs.

Engage Third party for vulnerability assessment.

The idea is that these third parties, which may be individuals, are knowledgeable in carrying out

the attacks and they will design the test cases to break your APIs and you will get a report.

So especially if you are exposing APIs to the public developer, this is something that you must consider

continuous monitoring to detect attacks and threats.

Carry out monitoring, check your logs, run reports on your logs to understand if there is any attack

happening.

If there is an attack happening, then you have to proactively take care of protecting your API from

the attacks.

The hackers are coming up with new type of attacks.

You need to be aware of it.

You need to be ready to tackle those new attacks.

Last but not the least, implement threat protection in the proxy for your APIs.

Almost all API management products today offer some kind of policy, some kind of mechanism to implement

the functional threat protection in the proxy.

Let's check the policies available on apigee for threat protection.

So here, as you can see, these are the security policies.

And I can see that there is threat protection, Json, threat protection and regular expression protection,

which can be applied to the proxy.

So here is a general idea on how security may be implemented for public or partner facing APIs using

the API management service or platforms.

You have a trusted environment protected by a firewall.

So what you can do is on the edge of your enterprise firewall.

You put together the API management Gateway platform.

You define your proxy that connect to the API implementation in your trusted environment, and then

you apply policies on the proxy for managing the security.

The API consumer invoked the proxy on which the policies are applied, and if everything is good then

the call is forwarded to the API implemented on the internal servers.

Suggestion is that you consider an API management platform for building public facing secure APIs.

Best practices for security.

Educate your developers on what they should look out for in terms of securing their APIs.

Provide some development guidelines, some patterns on implementing security for the APIs.

Hackers are always looking for vulnerabilities.

Stay up to date on security threats caused by any new vulnerability discovered by the hackers.

Have a well-defined security policy.

Define a contingency plan.

So you like it or not, At some point you may be caught off guard by an attack.

What would you do in that scenario?

How would you protect your API from an ongoing attack?

Have a plan for it.

Although as an API provider, you may not be responsible for all the infrastructure components, but

it is your responsibility to ensure that all the touch points within the API path length are secure.

So work with the other stakeholders in your enterprise, such as the network team, the server team

to protect the infrastructure, to protect your APIs from any kind of attack.

In this lecture I covered the API security from API management perspective, Key secret management is

not easy to build.

You need to consider some third party product to implement the key secret based security for your APIs.

Auth servers need to be integrated with your Ldap and you need to have some mechanism for token storage.

Again, the idea here is to leverage some off the shelf or two server rather than trying to build it

in your APIs.

Functional attacks are the most common form of attacks against the APIs.

You have to be prepared for functional attacks.

Testing is one thing that will prepare you for any functional.

The idea here is mimic scenarios of functional attack in your testing.

Hire a third party to carry out vulnerability assessment for your API and then continuously monitor

your APIs for any attack or signs of attack.