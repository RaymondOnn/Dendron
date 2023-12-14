---
id: wyr2o9xdscyz7yr3rpysuqm
title: 41_WalkthroughPagination
desc: ''
updated: 1699967262544
created: 1699965526968
---
Demo for pagination.

In this lecture, I'll walk you through the code for Acme API for hotels in which I've built the pagination

support.

I'll be using the code available in the pagination branch of the rest API course v2 repository.

Assumption is that you have already set up the repository locally and added documents to hotels collection.

If you face any issue with the DB connectivity, please make sure that you have set up the DB parameters

in the DB slash cloud db dot js.

I'll take the offset based approach to implement the pagination support in Hotels API.

And fortunately MongoDB provides the skip function which is equivalent to offset.

So if you use the skip function, you can actually point out the starting row from where you will receive

the data.

And the limit function allows you to limit the number of rows.

So we'll make a change to the DB object for hotels to make use of skip and limit.

Let me show you the behavior of the API before I walk you through the code.

Now this is the call to the API without the limit.

Let's put a limit of three and we receive three rows and I can change it to whatever number I want.

Five.

We can also specify the offset, let's say offset zero.

So offset zero is motive offset one is Western, let's try offset one and it is the first row.

Now there are two files of interest in this lecture.

The API implementation, which is in the hotels dot js file.

It reads the pagination parameters for the API call and then the hotels dot js under the DB that uses

the skip and limit for MongoDB to support the pagination implementation.

Let's go over the update made to the API implementation in hotels dot js.

I made a change to it to take care of the pagination parameters, so I've created this pagination Json

object and then checking if a valid limit parameter is being sent and if it is being sent as part of

the API call, then I'm setting the pagination limit to whatever value is received.

Similar thing is being done for offset.

Once that's done, I'm adding the pagination option to my options and then passing it to the DB function

for select.

Now let's take a look at the DB function for hotels.

In the select method, the pagination parameters are received in the options.

I've created two local variables for limit and offset.

Checking if we have received the pagination limit.

Parsing it to integer setting the local variable limit.

Same way I'm checking if the offset is received and if it is received.

Putting it in the offset local variable and then in the find method using the limit and skip with these

local variables.

So limit will restrict the number of rows that MongoDB will return and skip will be the function used

for setting the first row in the returned data.