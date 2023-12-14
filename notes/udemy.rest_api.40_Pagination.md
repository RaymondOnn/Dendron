---
id: 8imldjx9d88eyyfqsya3xc9
title: 40_Pagination
desc: ''
updated: 1699967235255
created: 1699965508572
---
Rest API pagination.

In this lecture I'll cover the concept of pagination.

At the end of this lecture, you should be able to answer why you should support pagination in your

rest APIs.

Also, you should be able to describe three common implementation patterns used for building pagination

support and rest APIs.

Pagination like partial responses.

Pagination also gives control of the responses to the app developer or the consumer.

In the case of an API that supports pagination, the app developer can decide the number of rows they

would like to receive in the response.

Consider this example of a browser based application that is invoking the hotel's API.

Now Hotel's API is returning a collection of hotels, and the browser based application developer may

decide to get 50 rows because they have more real estate to display 50 rows.

On the other hand, we have the mobile app developer who's using the same hotel's API.

They would like to see only ten rows because of the smaller real estate or the smaller screen.

Let's take a little bit deeper into the implementation of the pagination.

So the API has access to all the hotels data when the app developer makes a call to Hotels API.

The there is no specification around how many rows the app developer is requesting for.

So in this scenario, all the rows in the collection will be returned as response.

If the app developer specifies the number of rows they are interested in by way of page two.

For example, here, don't worry about how the specification of how many rows to be returned is done.

We will cover that later.

In this scenario, the API distribute the hotels data into multiple pages and then since the app developer

has requested for page to page two will be returned to the application.

In other words, the number of rows is restricted to a page.

So why use pagination?

The benefits of pagination are very similar to that of the partial responses.

You get better performance and optimise resource usage, less CPU and less memory usage and lowered

requirements from the network bandwidth perspective.

You can use a common API version for all your consumers to support multiple devices, multiple use cases

and multiple form factors.

There are three common ways in which rest APIs implement pagination.

The first one is the cursor based.

This approach is taken by Facebook.

Next one is the offset based.

This approach is taken by LinkedIn.

In fact, offset based is the most popular approach taken by most of the popular API providers.

In fact, Facebook provides both the cursor based and offset based implementation.

Third one is the use of Http header not so popular but used by GitHub.

I'll go through each of these in details now.

Cursors for pagination.

I've taken the content for this part of the lecture from the Facebook developer page.

If you're interested in knowing more about the content that I'm discussing here, I would suggest that

you go through the documentation for the Facebook APIs.

Let's start with the definition of a cursor.

A cursor is a control structure that enables traversal of records.

Cursor based pagination is considered to be most efficient.

Let's see how the cursor works now.

So a cursor is a random string that points to a specific item in a collection.

So for example, there is a hotels collection and you have a cursor which is a random string that is

pointing to one 0 to 9 nine.

So this is just the definition of a cursor.

When the API is invoked, it sends back an envelope based response.

So this response not only have the data for the endpoint, but it also has some metadata for paging

here.

In this response, we see that the cursor before is pointing to the beginning of the page received in

the response and after is pointing to the end of the page that was received.

The previous cursor points to the previous page, which is page one and the next cursor points to the

page three, which is the next page.

One thing I would like to point out here is that you don't have to use the envelope based approach if

you want.

You can use the Http headers for sending the paging metadata.

Offset based pagination implementation approach is the most commonly used approach for building support

for pagination in rest APIs.

Typically, this approach involves the use of query parameters.

The app developer has to provide the starting row by way of a parameter and the number of rows they

would be interested in receiving.

That's by another parameter.

So in this case, the offset equal to six is the starting row.

And the limit equal to five is the count of rows that the app developer would like to receive from the

API.

So in this particular scenario.

The number of rows that will be returned is five.

So rows six through row ten will be sent back to the app developer.

Let's take a look at some popular APIs that use this approach.

LinkedIn.

LinkedIn.

The app developer can call this people API with the offset that is referred to as start parameter and

count, which is the limit.

Facebook uses offset and limit.

Twitter takes a slightly different approach.

Instead of using offset and limit, it basically requires the app developer to provide the page number

and Rpcp stands for rows per page.

So think about it.

The page equal to three and equal to 25 can give you the offset for page number four.

So the idea is that the approach is same.

Just the semantics are slightly different.

Http link header based implementation.

This implementation is based on the http link header described in the RFC 5988.

For this discussion.

Think of link header as a way to create relationship between resources.

Let me describe it with an example.

GitHub exposes an API for searching the repository for specific phrases.

When you invoke this API, it responds back with the data which is paginated and the Http header.

In the http header you would see the link header.

This link header has two components a URL and a relationship.

So this particular part of the link header.

Gives you the URL to the next page and shows the relationship as next.

And this one here is the link to the last page.

The relationship shown here is last.

This is not a very commonly used approach, but if you would like to go ahead and explore it, I suggest

that you read through RFC five nine, eight eight.

Let's summarize by way of pagination.

You can provide your API consumer control on the number of rows they would like to receive.

Like the partial responses, your API with pagination support will have better performance and will

lead to optimal resource usage.

There are three ways in which you can implement pagination.

These are the common ways cursor based approach in which a cursor mechanism is used.

This mechanism is in use by Facebook APIs offset based approach.

Most common approach taken by LinkedIn.

In fact, Facebook also and the link header approach not so common used by GitHub.