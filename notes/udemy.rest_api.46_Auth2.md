---
id: trj2kkpj6fo7xkdmj56wvvd
title: 46_Auth2
desc: ''
updated: 1699967398872
created: 1699965659802
---
Rest Security.

OAuth 2.0.

OAuth 2.0 has become the de facto standard for building authorization for rest APIs.

Facebook, LinkedIn, Spotify.

They all use OAuth 2.0 for authorizations.

In this lecture, I'll cover the OAuth 2.0 framework and also I'll cover the design decisions you will

have to make in order to build authorizations for your APIs using OAuth 2.0.

Let's assume that Acme's vacation site has become very popular and so the customers are going to the

Acme applications, putting in their blogs, their views on the various vacation packages, putting

in some personal information around the preferences, which vacations they would like to go on.

They can also write blogs and add pictures to the vacation packages.

Now, this is all well and good.

Acme can analyze this information to create a personalized recommendation around the various vacation

packages that Acme will be able to sell to these customers.

At the same time, this data is of interest to Acme partner.

So let's say dreams who is Acme partner would like to create an application that sends out email offers

for vacation packages based on the preferences of the customer.

They have the knowhow to carry out analytics on the customer data to create very accurate vacation packages

that can be sold to these customers.

So dreams as acme to provide access to the data.

Now this is all well and good technically, but should Acme be providing this access to dreams?

The answer is obviously no.

This is not acceptable and the reason is because the data belongs to the customer.

Acme does not own that data.

So in effect, the dreams should ask the customer if it is acceptable to use that data.

Let's see how that flow will look like.

For the moment, let's forget about the technical implementation, but let's just focus on the View.

Let's say Dream has a way to ask the customer if the customer is okay with the dreams accessing customer's

data.

If the customer says yes, it's okay, then Dreams calls the API for customer information, along with

the proof of authorization from the customer.

Acme checks the proof of authorization, and if the proof of authorization is good, they let the dreams

request go through and access the customer information.

This, in effect, is the way the authorization works.

Or 2.0 is a flexible authorization framework.

It is defined in RFC 6749.

Let's go over some of the key aspects of this framework.

The OAuth 2.0 is based on tokens and there are different type of tokens used in this framework.

Five methods are described for acquiring access tokens.

These methods are also known as the grants.

End user is in full control of their data.

There is a concept of scope in which the user can decide what data the user is authorizing the application

for.

Application client needs a API key and secret.

OAuth 2.0 is the de facto standard that has been adopted by multiple providers.

In this lecture I'll be using Spotify as an example.

The authorization Scope grant is the most commonly used method for authorization.

It is also referred to sometime as the social login screen.

Example is you can use your Facebook credentials to provide access to a third party application to your

Facebook data.

This flow has three parties involved.

The first party is the end user who owns the data.

Second party is the application or the client who is going to be the consumer.

Third party is the provider of the API that has two parts.

First is the authorization server and the second is the resource or API server.

The flow begins with the application asking the user to grant the authorization.

The user looks at the scope and grants the authorization.

The application receives the authorization token as part of step two.

The application at this point sends the authorization grant to the authorization server.

The authorization server looks at the authorization token.

If everything is good, it responds back.

With the access token, the client or the application can then repeatedly use the access token to access

the protected resources.

The access token has an expiry, so at some point the access token will become invalid and the whole

flow may need to be repeated.

And then there is also a refresh token that may be used without repeating the whole flow.

Before proceeding any further, let me summarize the type of tokens I've discussed so far.

First is the authorization token.

The authorization token is a proof of authorization by the user.

It is issued after the user logs in, reviews the scope and then authorizes with the credentials.

Next one is the access token.

Access token is used by the application or the client to get access to the protected resources.

Some providers may define multiple types of access token.

So Facebook defines multiple types of access tokens.

The idea is that these different type of access tokens are associated with different scopes.

You can limit which can be invoked with the access token.

Access tokens have an expiry which is set by the issuer or the API provider.

After the access token has expired, the client has to do something to get a new access token and that's

where the refresh token comes into picture.

As part of the authorization flow, the provider not only provides the authorization token, but it

can also provide the refresh token.

And after the expiry of the access token, the refresh token may be used for extending the access tokens

validity or for issuing new access tokens.

As mentioned earlier, Facebook defines four different types of access tokens.

Keep in mind these are not standard access token types.

These are defined by the provider.

The action you can take on the user data depends on the type of token you're using.

I suggest that you go through the Facebook API documentation to understand how you can use these tokens.

Spotify is a music streaming service.

Spotify is supported on multiple devices, not just mobile phones and computers, but also on PlayStation

and other gaming consoles.

The users of Spotify can create free or paid account and they can listen to music, manage playlists

and provide comments and reviews on certain songs and artists.

The reason why Spotify has become so popular is because they provide API for developers and any developer

can register with Spotify and use their APIs to create applications on multiple devices.

Let me show you some of the applications which are quite popular and are using the Spotify APIs.

So I'm on the Spotify developer portal and these are the applications that are using Spotify APIs.

BBC.

Shazam is a very commonly used application, and then there are some very interesting applications like

Artist Explorer that allow you to explore the artists in a graphical format.

In the rest of this lecture, I'll be using Spotify OAuth implementation as an example to explain how

OAuth works.

Spotify supports four of the five grant types.

The only grant type that is not supported is the resource owner Credentials Grant.

And there is a good reason why this is not supported.

I'll explain it to you when I get to the resource owner.

Credentials.

Grant Description.

So the first one is the authorization scope grant.

Once the Spotify client application has received the authorization, they will be able to access the

private data for that user who provided the authorization.

The authorization scope grant flow requires the client to provide the secret key and the authorization

scope grant flow for Spotify.

Also returns the refresh token that the client can use for Revalidating the access token.

I've already covered the authorization scope grant, so I'll not be covering it for Spotify.

Suggest that you go through that flow on your own.

Next one is the client Credentials Grant.

When app developer registers with Spotify, they receive a client ID and a client secret.

They can use the client ID and client secret to invoke some of the APIs on Spotify.

There is no authorization involved since there is no authorization involved from a user.

The user data is not accessible to the client, so with client credentials you can only get access to

APIs that are returning public data.

Now, if you're thinking why would you need it, there are applications which just need the public data.

For example, I showed you an application that gives the graphical view of the artist.

Here the application is simply going out, collecting data about albums and singers and showing the

information in a graphical format.

The client credential grant flow is very similar to the token based authentication flow.

Let's go over it.

The application request access to the token and provides the credentials which are the client ID and

the client secret.

The Spotify account service returns the access token.

And then the client can use that access token to make calls to the APIs on Spotify.

Let's summarize what you can do with the client credentials.

Grant.

You cannot fetch the data for a specific user.

You can only fetch the public data.

You need to provide the client ID and the client secret for getting the access token.

And since there is no authorization flow involved, there is no refresh token provided to the client.

As a result, the access token cannot be refreshed.

Next one is the implicit grant.

You will use implicit grant in scenarios where you cannot protect the client secret.

This can happen if you are writing, let's say, a single page application that will run on browser

and you're using JavaScript for it.

Now, in that scenario, if you have the client secret embedded in the code, it is easily accessible

to a hacker or anyone.

Same thing is true for even mobile application.

If you are embedding your client secret in a mobile application, it's not protected.

The implicit grant flow does not involve any authorization.

You directly get the access token and with the implicit grant, you get access to the user data.

Let's go over the implicit grant flow for Spotify.

When the user takes an action on the client or the application, the application initiate authorization

request to access data by invoking the authorized endpoint on Spotify, we have to provide the client

ID and the scope.

The Spotify account service displays a dialog box that prompts the user to provide the login.

The user provides the credential and logs in.

At that point, the Spotify account service redirects the application and passes the token in it.

Once the token is available to the application, it can use it for accessing the Spotify web APIs.

Let's summarize what you can do with the implicit grant type for the Spotify API.

You can access the user data and you do not have to provide the secret key to get the access token.

And the access token cannot be refreshed.

Refresh token grant As part of the authorization scope grant flow, the client receives the access token

and the refresh token.

The client can save the refresh token in a persistent store for later use.

The client continues to use the access token for making requests to the protected resources, but at

some point the access token gets expired and when that happens, the client can use the refresh token

to get a new access token for the refresh token grant.

Everything that is applicable to the authorization scope grant applies because it's part of the same

flow.

Except that in the case of refresh token grant, you would use the refresh token to get a new access

token resource owner credentials grant in the resource owner credentials grant type, the user has to

share their credentials with the client or the application.

This obviously can lead to misuse of the credentials by the client application, and that is the reason

it is not supported by Spotify.

They want to protect their users credential from unauthorized usage.

You may still use the resource owner credentials grant type if you are dealing with trusted applications,

for example, for the Acme travel, if they are creating a mobile application and it is their own application,

they may use the resource owner credentials.

Grant.

But as a rule of thumb, my suggestion is not to use this grant type.

Spotify developers have done a great job in documenting the scopes.

I've taken this from their developer website.

I would suggest that you go through the scopes to get a better understanding of how you can control

the scope in your own APIs.

So here, as you can see, the first scope, which is represented by a dash, says that all publicly

available information on the user will be made available and this can happen with the client credentials

Grant.

Then there is a list of scopes which you can go through here.

For example, playlist Read Private is basically saying that the authorization for this scope will lead

to the client access to the playlist, which is private to the user or playlist.

Modify public.

With this scope, the client will be able to modify the public list maintained by the user streaming.

This will allow the client to control the playback.

For this, you will need to have authorization, scope, grant or implicit grant or a refresh token

grant.

As an provider, you have to make certain design decisions for implementing OAuth.

The first one is scoping of the user data.

What data for the user is public and what is private?

Very specific to your APIs, very specific to your domain data.

Next one is the type of OAuth grants that you would support for your APIs.

Typical ones for private data are authorization and implicit grant.

Suggestion is to stick to this client credentials can be used for public data.

Now, from the travel perspective, vacation package data is public data.

And the, let's say the email address of the users for the vacations packages is the private data.

So you would expose the email only if the authorization or implicit grant is used, whereas you can

expose all the vacation packages or the reviews by the users, by way of even client credentials.

One other thing I talked about earlier was that you may use the resource credentials for trusted applications,

but suggestion is to stay away from it.

Implementation of the OAuth server is a complex task.

Although you can use libraries and packages such as Passport or OAuth strategy to build your own OAuth

server, but my suggestion would be to consider an API gateway or an API management platform for building

OAuth in front of your APIs.

In this lecture I covered OAuth 2.0.

OAuth 2.0 is a authorization framework.

This framework has been adopted by multiple providers such as Facebook, Spotify, LinkedIn and at this

point it has kind of become a de facto standard for implementing authorization for APIs.

There are five different grant types defined under OAuth 2.0.

These are the methods in which authorization is carried out.

The first one is the authorization scope grant.

In this, the user has to provide an authorization for the scopes requested by the client.

Once the authorization is granted, the client uses the authorization token to get the access token

and also the refresh token.

Next one is the implicit grant.

In the implicit grant, the authorization flow is not needed.

The client provides the credentials and the authorization is carried out by the server and an access

token is issued to the client.

Next one is the resource owner credentials.

Resource owner credentials require the user to provide their credentials to the client.

This is not suggested.

Spotify does not support it.

Many of the other providers do not support this grant type.

Next one is the client credentials.

Some of the data that is available is public in nature so that data can be provided to the client without

the need for any authorization from the user.

So then we can use the client credentials grant under which the client has to provide only the client

ID and the secret, and sometime even the secret is not needed.

The last one is the refresh token grant, which is part of the authorization scope grant.

When the authorization scope grant happens and the client receives the authorization token, they can

use that token for getting a refresh token.

That refresh token can then be used for creating or revalidating the access token.