---
id: o6i0e17pirzzq9x69nmeyi6
title: 43_BasicAuthentication
desc: ''
updated: 1699967317968
created: 1699965580385
---
Rest API security Basic authentication.

In this lecture, I'll cover the concept of basic authentication for API security.

You will learn about some of the issues related to basic authentication scheme.

Also, I'll walk you through the code for implementing basic auth for node API.

Basic authentication is the easiest way you can protect your APIs in basic authentication.

The consumer sends the credentials to the API server in the Http header called authorization.

The data in the authorization header is a base64 encoded string.

The format of the string is user column password.

The server on receiving the credentials in the Http header decodes the value in the header and then

checks the user and the password.

If everything is good, it sends back a 200.

Okay.

If the user ID and password are not good, then it sends back a 401 unauthorized.

If you are using Http, that means you are sending the data in clear text or plain text format.

Anyone can read that data.

That means the user ID and password that are being sent as part of the authorization header are going

to be visible to anyone who is carrying out a man in the middle attack.

So the bottom line is basic authentication is okay to use with SSL or Https, but not good to use with

the Http basic authentication scheme has some inherent issues.

Let me explain to you were these issues with some examples?

Let's say Acme Travel has partnered with a company called Dreams and Dreams, runs a website that lets

the users bid on vacation packages.

When the user.

Needs information on the vacation package.

The website for Dreams will allow the user to go through this path.

Here, as you can see, Dreams is making a call to the Acme API using the credentials that have been

provided by the Acme Engineers to the Dreams engineers.

This is all well and good, but as explained earlier, if Dreamz is using Http, then the man in the

middle attack is possible.

The cleartext credentials can be read by anyone who has access to the data flowing from Dreamz to Acme.

So this is one issue.

The other issue is that basic authentication requires the caller or the consumer to send the credentials

in every request.

Now, to make things easier, Acme engineers may decide to use sessions, but that's not allowed from

the rest perspective.

So this is another problem.

The caller has to send the credentials with every request.

Another issue is with the mobile applications that may be directly invoking the APIs.

So let's assume that Dream's engineers decided to build a mobile application for vacation bidding.

In this scenario, the vacation bidding application is expected to go directly to the Acme API server.

This would require the Dream's engineers to hardcode the credentials in the application and anybody

can break into the application and take out the credentials and use it for whatever purpose.

So these are some of the common issues that are highlighted for the basic authentication scheme.

Let me walk you through the code for building APIs that support basic authentication.

The code for this demo is available on GitHub.

In this NodeJS demo.

I'll be using an NPM package called Passport.

You can read about passport@passport.org.

The passport package provides an authentication middleware.

It's non-intrusive.

You really don't have to put if then else statement in your code.

So the code becomes very maintainable.

It supports multiple forms of authentication, basic authentication, just one of those oauth2 tokens,

you name it and it's supported.

There is a built in support for social authentication.

Obviously that aspect is more for the consumer.

Not from the provider perspective.

And at this point there are over 300 strategies available from authentication perspective in Passport.

So I would suggest that if you are into node programming, look into this package.

It seems to be very effective and highly supported in terms of implementing any kind of authentication

you may be looking for.

So for any authentication, you need to think about where you are going to store the user information,

whether it's going to be in a database, in a file system or on Ldap.

In this demo I have stored the data in a file called Users Dot JS.

It's hardcoded.

It can easily be changed to a database.

You can store all your users there.

And in this users dot JS you will find a function called check credentials.

And what it is doing is it is simply getting the username and password and checking if the username

and password exist in the users array and that's what it is doing.

So to protect the resource here in this case, slash private, I'll need to insert the passport basic

authentication middleware callback.

So that's what this auth is for here.

What's happening is that when the get is called on slash private, the first callback that will be called

is auth and auth will check if the credentials are good.

If the user is good, if the credentials are good only then this main function will be called for getting

the resource back to the caller.

The basic auth setup is done in a file called basic auth.

So next we'll look at that.

So in basic auth you will find two packages from passport perspective.

Passport and passport http for this demo I'll be using Http, not https.

That's the reason I needed passport http.

I'm indicating here that I'm interested in basic strategy, and then the user's data is also imported

here from the strategy creation perspective.

You need to provide a function, a callback function that takes username, password and a done callback.

And here, if the user is found, we make a call to the done callback with a true.

Otherwise we make a call to the done with a false and then the auth middleware function is created by

calling passport dot authenticate and using basic here to indicate that it's basic the session management

false indicate that there will be no session management.

Let's start the app and try it out.

First, I'll hit the private resource URL with the browser and as you can see, we received a dialog

box indicating that a 401 unauthorized header was received.

So here we can provide the password and the user id.

I know the password.

The user ID is Sam and Sam.

One, two, three.

And as you can see, access was granted to the private resource to dig a little deeper into what's happening

under the covers.

We will try the same URL with the postman client.

So I'm doing a get on this resource.

Obviously I get back a 401.

Unauthorized and unauthorized message in order to provide the authentication credentials I'll do.

Select the basic auth and provide the ID and password.

And just do a send.

Now, this time access was granted.

Now, if we look at how the postman client is working.

It'll give us a better idea.

So let's go to.

The headers and under headers you will see the authorization header which has basic and then it has

a weird looking string.

Let's copy this string.

Now, this string, if you remember, is the string that has the base64 encoded user ID and password.

So what I'm going to show you next is we will decode this string to see what's there.

So I'm on a website which allows you to decode base64 strings.

So I'm just going to paste the string here and.

See what we get.

Decode.

And as you can see, we received Sam Colon.

Sam one, two, three, which is the user ID column password.

Let's summarize in this lecture.

I covered the use of basic authentication for securing APIs.

Basic authentication is part of the Http protocol specifications.

The specification requires the request to use the Http header called authorization.

The authorization header carries the credentials in the base64 encoded format.

A couple of things to keep in mind.

You must use Https for basic authentication because you don't want the credentials to be visible to

anyone.

So with Https, the credentials, the headers and the body is encrypted, so the man in the middle attack

will not be possible.

The basic authentication scheme requires that the requester of the API pass the credential in every

call because you cannot manage sessions or you should not manage sessions in the rest API.