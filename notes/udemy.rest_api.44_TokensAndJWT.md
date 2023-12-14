---
id: qj9ylx744bdb2cmgit3wj74
title: 44_TokensAndJWT
desc: ''
updated: 1699967343380
created: 1699965603257
---
Rest API security, token based authentication and jot.

In this lecture, I'll cover the concept of token based authentication.

At the end of this lecture, you should be able to explain what tokens are and you should also be able

to explain the standards for token.

The Jot standard JavaScript web tokens.

At the end of this lecture, I'll also walk you through the code for building node APIs with the jot

based authentication.

Let's consider the same scenario we discussed about basic authentication and dreams.

Dreams has to authenticate with the ATM with token based authentication in this scenario.

The very first thing that the web application on the dreams end will do is it will invoke a token endpoint

with the credentials Acme on receiving the credentials will validate the credentials and then issue

a token after issuing a token.

It would save that token in some kind of a storage.

Now don't worry about what a token is.

We'll cover that.

Once the token is issued, Acme will send back that token to dreams.

Now all subsequent calls that dreams will make to Acme, let's say slasher source has to be accompanied

with the same token that was issued to it.

Acme will validate the token by checking the storage and will let the call go through with 200.

Okay.

Or if there is any issue with the token.

For example, if the token has expired or if it is invalid, it will respond back with 401.

So in basic authentication, I discuss one problem where the dreams engineers decided to create a mobile

application and if Acme was offering its APIs using basic auth, they had to keep the credentials on

the mobile application.

Obviously anybody can hack into the application and steal the credentials so it is not good.

Token based authentication solves this problem.

Let me show you how.

Dreams invokes the slash tokens with the credentials.

The Acme server will issue the token and store it in the local storage.

After that, dreams on receiving the token will pass that token to the mobile application.

The mobile application will now invoke the Acme APIs using the token.

The idea here is that dreams engineers now do not have to hard code the credentials in the mobile application.

Also, the dreams engineers are in total control of the token and in a later lecture you will learn

how the consumer can control the tokens.

Now the big question what is a token?

A token can be thought of as an encoded string.

So there is some information that goes through some hashing or private key encryption, and that is

what a token is.

The API does not need to manage any sessions.

The client can send the token, which is just a string in the Http header or in the query parameters

or in the request body.

The issuer of the token can manage the validity of the token, for example expiry.

The issuer can decide when the token will expire.

In other words, how frequently the client has to validate their credentials.

Also, revocation the token can be revoked at any point in time by the issuer.

There are various ways to create the token.

One of the standard around token creation and management is Jot, which stands for Json web tokens.

Today, multiple providers are supporting token based authentication.

So let me walk you through the overview of Jot or Json web tokens.

Json web token looks like this.

There are three parts to it.

The first part is the header separated by a dot.

There is a payload.

The payload has what is known as the claims.

And the third part is the signature.

The header has some metadata, which is base64 encoded, and that is the first part.

The payload is again Base64 encoded.

Second part signature is basically a hashing of header and payload along with a secret.

And that's the third part.

Jot is self-contained because the payload contains all the information about the user and the token.

We'll cover that in details.

It's Json based and you can read more about it in RFC 7519.

Let's go through the details of each part now.

Header has the metadata.

Type indicates jot.

This is a fixed string and the hashing algorithm.

There can be multiple hashing algorithms that can be used.

So the metadata in the header is just the type and the hashing algorithm.

This is an example of the header.

This is passed through the base64 encoding and that's what the header looks like after Base64 encoding.

Payload is the heaviest part of the token.

It has bulk of the information payload contains the claims, which basically are nothing but Json attributes

or elements.

There are three types of claims which are contained in a payload.

The first one is the registered claims, which is a standard set of claims or attributes.

Issuer is an example.

Next is the expiry timestamp, the which is the not before timestamp and there are multiple other registered

claims.

Next type of claims is the public claim.

Public claims are the attribute names such as name, email, phone number and other attributes that

identify the consumer or the user.

Anyone can suggest new public claims.

So there is a registry for public claims or the public attributes that can be used for deciding what

public claims you want to include in your token.

The third type is the private claim type.

These are not standard.

As the name indicates, the consumer and the provider can decide on what claims to include in the payload.

There is a risk with the private claims that the claims in under the private can collide with the public

or registered claims.

So you have to be very careful in using private claims.

This is an example of the payload.

The payload goes through the base64 encoding and that's how the second part, which is the payload gets

created.

Let's take a look at the registry for registered and public claim.

The link is in the resources.

As you can see, you will find all the public and registered claims here.

Issuer Subject.

Audience Expiry Date.

These are the registered claims under the ISG.

And then rest of the claims that you see here are the public claims.

The secret is created by concatenating the base64 encoded header with the Base64 encoded payload and

then hashing this string with the secret secret is the key to the success of a token based authentication

mechanism.

The provider has to keep the secret confidential.

If the secret is leaked to anyone unauthorized, they can impersonate the provider and that's obviously

not good.

The output of this hash function is the third part, which is the signature.

In this demo, I'll show you how to implement simple authentication for rest APIs in Node.

The solution is available on GitHub under the branch tokens for Node, there are multiple packages available

for building APIs with the Jot support.

I will be using Jot simple package.

There are two flows that I'll walk you through.

The first flow is where the consumer invokes the token endpoint with the credentials in the body.

These credentials are then used within this flow to issue a token which is stored to a in-memory token

store.

This is the first flow.

The token is then returned to the caller.

In the second flow, the caller will invoke the slash private endpoint with the token in the header.

This token is then validated against the token store.

So these are the two flows and the code that I'll walk you through.

Let's walk through the code for creation of the jot tokens.

Jot auth JS is the file where you will find the code.

There are a bunch of parameters.

Jot token secret.

This is a random string that represents the secret.

Then there is a token issuer which is Acme Travel, the entity that is issuing the token.

Acme token header x Acme token.

This is the header that will be used by the consumer to send the tokens.

Then there is a token expiry.

The token expiry is the time for which the token is valid.

The main function for the creation or issuance of the token is auth.

In this function, the very first thing we check is if the consumer has sent the body with the right

data, the credentials.

We extract the credential and check against the user data.

If the credentials are good, if the credentials are not good, then a 401.

Unauthorized is sent back.

If the credentials are good, then we are going to create the expiry which is the 30s after the time

now.

Create the payload.

In the payload, there are two registered claims expiry and the next one is the issuer.

There are two public claims.

Name and email.

Once the payload is created, we are we are simply dumping it on the console just to see, you know,

what the content of the payload.

Next we are calling the jot dot encode with the payload and the secret.

This will encode and create the token.

Now one thing you will observe is that there is no header here and that's because Jot Simple Package

is taking care of it for us.

After the token is created, I'm adding the token to the token store and then sending back the response,

which is the token in the app file.

I've added a new route for creation of the token.

First, we are creating a auth auth which is the token issuer and then adding it as the middleware function

or middleware callback within the slash token resource path.

Let's start the app now.

I'm on Postman, I'll hit the private URL, which is a protected URL and I get a 401 unauthorized token

not provided.

So now the next thing we are going to do is request for a token.

Token is available at this URL.

And now what will happen is if I send it without providing the body again, I'll get the 401 unauthorized.

So we need to provide the body here, which is Json.

So I've provided the name and the password.

Now let's try it out.

And this time we received the token here.

Status is 200, and here is our token.

Let's analyze this token.

I'll just copy it.

I'll do the base64 decoding.

And this is the decoded token here.

The first part is the header.

Second part is the payload and third part is the signature.

The private resource is protected by way of the auth middleware.

Callback function for validation.

So this validation flow is invoked for private before the actual function is invoked.

So I'll walk you through what happens in the validation of the token.

Token validation code is in validator dot js here.

Auth function is the function that gets called when a call is made to slash private.

Very first thing we do is get the http header exact token.

That's where the token should be.

If the token is not found, then we simply send back a status of 401 and a message saying token not

provided.

If the token is provided, then the token is decoded.

Any issue with the decoding of the token will lead to an invalid token message with response code of

401.

Now one thing to observe here is that decoding will require the token secret.

Next thing is, once the token is decoded, it has to be validated.

These validations are in the tokens is valid is the method here.

As you can see here we are checking if the expiry time is less than the current time.

That means the token has expired.

So the token is removed from the token store.

Also, if that is not the case, then a true is returned, which indicates that the token is valid.

If the token is valid, then a call is made to next, which leads to the invocation of the next method

in the callback functions and the next function.

Here is this one which leads to the sending of this message Access granted to private resource.

Let's see this whole flow in action now.

As a first step, we have to get the token by posting to localhost 3000 token and in the body we need

to provide the credentials.

Now send and we receive the token here.

So I'm going to copy this token.

As a second step, we'll make a call to the private endpoint.

And for that in the header we have to give the token in x dash x dash token.

Copy the value it sent and access granted to private resource.

Now, earlier when I was walking you through the code, I told you that this token has an expiry time

of 30s.

So if I keep on hitting this, we'll keep on receiving 200.

Okay.

Till the token has expired.

So I'm going to put the video on pause and then try it out again in a few seconds.

Almost 30s have passed.

And if I hit send now, I got 401 unauthorized token, invalid or expired.

Let's summarize in this lecture I covered the concept of token based authentication.

A token is an encoded string used for authentication.

George is a common standard for creating the tokens.

George stands for Json web tokens.

It has three parts header payload and signature.

Tokens may be set to expire.

Tokens may be revoked by the provider or the issuer of the token at any time.