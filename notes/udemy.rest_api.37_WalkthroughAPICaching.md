---
id: 99m6hnum1jgil2v24dm8zoo
title: 37_WalkthroughAPICaching
desc: ''
updated: 1699967138533
created: 1699965432890
---
Them offer Apia caching.

The primary intent of this demo is to show you how you can use the cache control directives in your

APIs.

There are two scenarios that I'll cover.

The first scenario is under which the provider will control the validity of the cache by providing the

max age.

Second scenario is from the Apia consumer perspective.

The consumer in the second scenario will be able to suppress the cache or override the max age and the

public directive provided in the scenario number one by using the no cache.

Let me walk you through the demo setup.

I have a simple NodeJS based API and a browser based frontend.

The browser based frontend is using jQuery for making Ajax calls to the API on the Node.js in scenario

number one.

I'll show you that when the call rest button is clicked on the browser, it leads to a call to the API,

and NodeJS at that point will increment a counter and also it will set the header cache control directive

max age equal to five in the response.

That response will go back to the browser and then browser on subsequent calls will get the counter

value from the cache if the cache is still valid in scenario number two, we will suppress the caching

on the browser based interface so that browser will always make a call to the backend and the counter

will always increment.

As always, the code for this demo is available on GitHub.

I would suggest that you clone it on your local drive and play with not just the cache directives that

we are going to use in this demo, but also other cache directives.

I've already set up the code on my local machine.

Let's open server.js.

This is where the API is implemented.

The endpoint is slash cache test and the very first thing that I'm doing in this code is I'm setting

the cache control header and the directive I'm passing is public and max age equal to max age, which

is a variable here.

So you can change this to change the behavior of the cache here.

Every time the call is received, the counter is incremented and initialise this counter to zero and

then send back the counter value to the caller.

So that's a very simple API and that's how this is behaving.

Let's go ahead and start the server now.

Let's go ahead and hit the API.

So in this interface, the this box here will show you the Http cache control directives.

And in this box, you will see the contents.

So let's go ahead and hit the API.

Call rest here.

As you can see, the Max Age directive is set to five and here the counter value is one.

Now, since the cache has expired, if I hit the this button again, it will lead to a counter value

of two.

And then what I'll do is I'll hit it and then I'll hit it again a couple of more times to show you that

while the cache is valid, the counter will not get incremented.

So this is the first hit.

It is still two second hit.

It is still two.

Third hit.

It is still two.

The idea here is that the browser is getting the counter value from the cache rather than going back

to the server.

Now, if I hit the call button, it will lead to the counter value of three.

So you can play with this server code and change this to different values to see the behavior.

For example, I can change this to to ten and see how the server will behave.

Now to walk you through scenario number two.

I'll quickly show you how the index.html is set up.

That's the HTML page.

So here you would see that there is a variable called suppress cache.

If the cache is suppressed, then I'm calling the Ajax with the header.

Cache control directive.

No cache, so this will suppress the caching on the browser.

Let's go ahead and try it out.

So here I'll simply click on this checkbox.

Now the cache is suppressed with no cache.

Now whenever I'll click on the cache button, it'll increment the counter.

As you can see, the caching is there from the server end, but since the cache is being suppressed

by the API consumer, that is the browser interface.

The counter is incrementing every time.

So if I click on this back, we are back to scenario one.

Now if I hit it 17 it stays on 17 for a couple of clicks.

I would encourage you to try changing the client and the server code for this demo and try out various

directives on your own.