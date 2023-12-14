---
id: f0lmucnpatle8ack8it3cjl
title: 45_KeyAndSecret
desc: ''
updated: 1699967366636
created: 1699965625769
---
Rest Security API key and secret.

In this lecture, I'll cover the concept of API keys and secrets.

I'll discuss what API keys and secrets are, how they are different from the user ID and password.

Also, at the end of this lecture you will be able to describe the design decisions related to the implementation

of a key and secret based security scheme.

API Key and secret.

If you want to use the APIs from, let's say, Facebook or Walmart or Twitter, you need to register

your application with the provider of these APIs and the provider after your successful registration

will issue a API key and secret like user ID and password API.

Key and secret are strings, but they're not the same.

The API key and secret are meant for applications or machines.

They are not meant for humans.

User ID and password.

These are strings that are meant for humans.

These are long random strings and they're not easy to crack.

The API key identifies an API consumer sometime.

It is also referred to as the client key or client ID, but the idea is the same API secret is used

by the client to prove its identity.

Client is the same as consumer.

It may be used as a password in basic authentication or for token based authentication mechanism or

the API secret may be used by the consumer to create a digital signature.

Let's go a little deeper into the digital signature.

Now let's revisit the example of Dreams and Acme.

In the case of basic authentication or token based authentication, dreams had to send the credentials

to Acme.

Now, with the digital signatures, dreams will not send the credentials.

Instead, it will send the API key and a digital signature.

The signature will be created by running some kind of hash function using the API secret on the header

or some payload.

Acme already holds the keys and secret for every consumer, so when it will receive the API key, it

will know who the caller is and the signature will be validated because Acme already knows about the

secret for the consumer on the dreams end.

So this is how digital signatures are used to prevent sending the passwords or the secrets.

Amazon AWS Rest Services allow the application developers to create anonymous access to S3 resources,

and the way they do it is the developer has to create a signature by using the secret.

This is the secret and by using the Http header as the payload on which the signature is applied.

And that signature is then base64 encoded on the Amazon end, they would know what the secret is, so

they will be able to apply the same secret to the Http header.

And if the result is the same then the user is authenticated and the S3 access is allowed.

So in what ways the key is used by the provider.

You've already learned that API key in combination with the secret is used for authentication.

Another use is user analytics.

Every time the API consumer invokes an API, somehow a log is created and an analytics entry is created.

At the end of the day, this analytics can help the developer and API provider understand how the APIs

are being used.

Another use for the API key is for getting tokens from the API provider.

So creating the anonymous access for the mobile application is an example where a token can be created

for that kind of a resource access.

Another one is rate limiting.

Now this is an interesting one.

Let's consider Twitter.

Twitter has the APIs which are used by the external applications as well as by the applications created

by Twitter.

So the internal applications, which are the applications owned by Twitter, can invoke the same APIs

and there is no restrictions on how many calls the internal applications can make.

Whereas if a public developer like you and me registered on the Twitter site to get an API key and secret

and use that key and secret, we can write an application against the Twitter API.

There is no problem, but there is a catch.

We are restricted to a call volume of 180 calls per 15 minutes and if we exceed that we will get a http

429 status code.

Too many requests.

I'm on the Walmart open API developer page and an.

Order to register for a key.

Just click on register, provide the name.

I have already provided all this information.

I already have an ID, so I'll just sign in.

Now, once you sign in, you will see the key.

This is my key.

And as you can see, the key has a rate limit restriction five calls per second or 5000 calls per day.

So I would suggest go ahead and register on this side and play with the APIs to get a better feel of

how this works.

Try to send more than five calls per second and see what happens.

The API provider has to make some decisions around the use of API keys and secrets.

The first one is how will the consumer send the API key and secret or signature to the provider like

tokens?

It can be sent in the http header.

Query parameters or request body API key and secret management.

Here we are talking about how will the API provider manage the keys and secrets?

For example, there is a need for a database for managing these API keys and secrets.

There is a need for building some kind of interface to invalidate the Keys or Secrets security scheme.

How will the API, keys and security be used for implementing the security API key and secret provisioning?

The application developer will need the API key and secret by somewhere.

So you have to think about how you would provide the API key and secret to the application developer.

Can they request it on a web page or would you send the API key and secret in an email?

Something that you need to think about rate limiting and analytics if you will be supporting, rate

limiting and analytics, How would you do that?

These aspects are a little challenging to implement in the API code.

In fact, I would suggest not to implement it in the code.

Instead, you should consider to leverage an API management platform that can easily take care of these

challenges as well as provide you additional features to make your APIs richer.

There are many such platforms available today.

I'll be covering the API management in a later section.

We'll go deep into what API management is and how it would help you manage your APIs without writing

code in your API itself.

In this lecture I covered the concept of API keys and secrets like user ID and password API keys and

secrets are strings.

The user ID and password is meant for human consumption.

People remember their passwords and IDs.

The API keys and secrets, on the other hand, are meant for consumption by machines or applications.

They are long strings of random characters.

API keys and secret is issued by the API provider.

The key and secret together creates the consumer identity, provide the security and also allows for

rate limiting.

There are multiple design considerations related to key and secret management.

The API provider has to decide how the consumer will provide the API key, how the consumer will use

the secret.

Will it be provided as part of the key secret in the basic authentication, for example, or would it

be used in the signature creation?

Also, there are other related design decisions, such as how will the key and secret be managed?

How the consumer will access or create the key and secret?

It is difficult to implement some of these decisions in the code.

In fact, it's not even suggested that you do that for that, that you can use the API management platform

and we will be covering API management platform in a later section.