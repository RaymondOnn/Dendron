---
id: poxanrdx77dm2xsrlg7a3hc
title: 32_HandlingChangesToAPI
desc: ''
updated: 1699967029297
created: 1699965269497
---
Rest API handling changes.

One of the things that you have to keep in mind for the software is that it is going to change.

There is no way you can avoid changes to your APIs or for that matter, any software.

So the discussion in this lecture applies to any software, not just API.

At the end of this lecture, you should be able to describe a breaking and a non breaking change.

You should be able to describe the best practices for handling API changes when an API is created.

It is initially geared towards fulfilling the needs of the internal applications of an enterprise.

In this case the API.

And anytime you change the API, you just have to work with the internal developers to basically manage

the change.

You know, the website doesn't break and everything is kind of transparent to the end user in terms

of any change.

It is a controlled environment where everyone knows everyone else and changes are easy to manage.

But as time goes by, the APIs are exposed to the external developers from external partners.

And now making a change to the API would mean impacting not just the internal applications but also

the external applications and interfaces that are using that same API.

So managing change in case of API become difficult because of the fact that you will be impacting not

just the internal stakeholders, but you would also be impacting the external stakeholders.

And you really don't know who all these external stakeholders are.

You cannot go to these external stakeholders and say, Hey, on so and so date, I'll be making a change.

So be ready to make changes to your code and on that day, just switch to the new version.

You can't really do that.

So let's consider an example.

Let's say there is an API that exposes the deals resource to the consumers, and when the consumer invokes

the deals API, they get back an array of discounted packages.

So there can be multiple elements that they are receiving in this array.

For example, the first package is Bahamas zero one.

It has a description, there is a price and how much discount the end user will get.

Now, the developer of the application who is invoking the API can simply access all the elements within

the discounts and then display the package name, display the description, etcetera.

Now, depending on the language or the platform in use, the code may look different, but this is just

to give a general idea.

So this is how the original looks.

Now let's say the provider gets a feedback from the app developers or the consumer that they would not

only like to receive the information about discounted packages, but they would also like to get some

links to the pictures related to those packages.

And the app developer, when they receive these pictures, can access the pictures using dot pictures.

Now, what is happening here is that the app developer who provided the feedback are able to get access

to the pictures.

But there may be other app developers who are not even aware that such change has been made.

Their code will stay the way it was earlier.

No access to the pictures.

The point is that those app developers are not going to be impacted by this change.

So this is a non breaking change.

The definition is simple.

If your change is not breaking the code of an existing application, then it is a non breaking change.

As you can guess, the opposite of non breaking change is a breaking change.

The definition is that if the change breaks down the application, the existing applications, then

it is a breaking change.

Let's say the provider for Deals API finds that there is a issue and the issue is that he has used discounts

instead of deals.

So there is a need to change discounts to deals.

Existing applications are using this code where they are saying data dot discounts, dot package data,

dot discounts, dot description to access the various attributes of the discounts array.

Now this code has to change and this code should look like data dot deals dot package data dot deals

dot Description.

Any application that does not change to data deals will break.

And so this is an example of a breaking change.

Let's take a look at some other common change scenarios.

The first one is you have an existing API to which you add a new operation or resource such as get hotels.

Now, this would not impact any existing API consumer or application.

Another one is adding optional parameters or properties here.

The example shows query parameter limit equal to three.

The idea here is that you are providing a way to your API consumers to limit the number of vacation

packages being sent back in response to the get call for vacations.

If the consumer does not provide the limit, some default is used so that way the existing applications

or API consumers who are not using the limit will continue to work.

These are examples of non breaking changes.

Next one is the change, the http verb or method.

Example here is that let's say you expose the creation of vacations as a put method.

So put off slash vacations will lead to creation of a new vacation package.

But you realize that that's not a good practice and you would like to change it to post vacation.

So obviously if you are going to change the existing put vacations to post vacations for creation of

a new vacation package, it would require a change in the existing application.

Otherwise they will stop working when you will make the change to the API.

Delete an operation example is you created an operation vacations by destination and you said, Hey,

this is not a good idea.

I want to replace it with a generic search that would allow the caller to set the destination as one

of the search criteria.

So what would happen is any application that's already been built with the access to this API will break.

So this is an example of breaking change.

An API typically exposes the back end databases or services to an application developer.

The idea here is that if there is a change in the database or the services that the API is using, then

the API code needs to change sometime.

All the changes that you are making in the API because of the changes in the dependencies can be absorbed

within the API.

In other words, it will not lead to a breaking change, but sometimes it may not be possible, in which

case the application developers will have to change the code of their application.

So it's a breaking change.

The idea is that all such changes in the backends can be either breaking or non breaking.

There is no hard and fast rule on that aspect.

Every scenario is different.

You have to take a look at your specific scenario to understand how you can absorb these changes to

make it non breaking.

That's a good practice.

The change can be major or minor.

A minor change is a change that does not require the app developer to make changes to the application

right away.

For example, change in some attribute length vacation package which used to be 50 character long in

the database change to 75 character.

In that case, the API can restrict the vacation package to 50 character, although it is receiving

75 characters from the database.

So the application developer doesn't have to take care of this change right away.

It's not going to really add any value.

Obviously it's good to have.

So this is an example of a minor change.

Example of a major change is let's say there was a discount calculation service.

That service now require an additional parameter for calculation.

So obviously the app developer has to provide that additional parameter, in which case the application

has to change.

So this is an example of a major change.

At some point, European code will need changes.

Before you commit to making any changes.

Evaluate if the changes are really adding some value.

If the answer is no, they're not adding the value, then maybe you should avoid changes.

That's a high level suggestion I have.

I'll share some common good practices for handling change.

Eliminate or minimize impact on the app developers.

Second one is provide some planning opportunity to the app developers.

You don't want to just go to your app developers and say, I'm going to make a change to the API and

you have to change your code within one week.

Not a good idea.

Especially external developers cannot be managed in this way, so you have to keep your developers informed

about the upcoming changes so that they can plan accordingly.

Third thing is support backward compatibility if possible.

So this is an example of a non-breaking change.

Fourth one is provide support to app developers with the changes.

For example, you can provide them documentation, you can provide them some sample code.

Minimize change frequency.

You don't want to make frequent changes to your app once per six months or per quarter is a good idea.

Version your API right from day one.

Let's summarize in this lecture.

I covered breaking and non breaking changes.

Breaking changes require API consumers to change or the applications to change.

Back end changes may be breaking change or a non breaking change.

It may be a major change or a minor change.

Major change requires the app developer to change their application code.

You have to follow some good practices that we have discussed.

The good practices we discussed requires you to avoid frequent changes to your APIs.

You are suggested to focus on your app developers.

How would they be impacted by this change that you are planning to make?

How can you minimize or eliminate the impact on the app developers?

All those aspects require you to think through the change and keep your focus on the app developers

for that or the consumers.

You must provide backward compatibility.