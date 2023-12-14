---
id: 7ctce6e5n50ny1inh5pyo7n
title: 103_SpecifyingTheResultFormat
desc: ''
updated: 1701194755966
created: 1701192813015
---
Instructor: In this section of the course,

we will be looking at how to control the query results

in various ways.

We'll start out simple by going through

how to format the results as YAML instead of JSON

and how to make the results pretty in the case of JSON.

Feel free to skip this lecture

if that's not something interesting to you

but I want to show you how to do it anyways

in case you're interested.

First, lets see how to make elastic search return YAML

instead of JSON.

I have already prepared a simple query

so all we need to do is to add a query parameter

named format with a value of YAML.

So lets do that.

Format equals YAML

And lets run the query.

And as you can see, we get YAML back.

Next, lets see how to format the JSON

that elastic search returns

so that its easy to read for humans.

But what do I mean by that you might think

because doesn't it already look good

and easy to read?

It does, but that's only because we've been

running queries within console.

Because it does this for us automatically.

After all, its a graphic user interface

so its very convenient that it does this for us.

If we run the query within the terminal however,

we'll see that the results are not formatted

in a very human readable way.

What I'll do is to copy the query as cURL

and then modify within the text editor.

I do that because I have to change the host name

to local host instead of elastic search.

That's just because I'm running elastic search

in Kibana within docker containers

and Kibana uses a host name of elastic search

to communicate with the cluster.

Anyways, lets copy the query as cURL

and I'll just open the text editor

and all I have to do is to replace

elastic search with local host.

You don't need to do this if you're not

running elastic search within the docker container.

And I'll also get rid of the format parameter right here.

Then I'll copy this and open up the terminal

and paste it in

and hit enter.

As you can see, the results don't look pretty at all.

And its not easy to see what's going on here.

Lets go back to the text editor and add a query parameter

named pretty to the code request.

So I'll just add a question mark and type pretty.

We don't actually need to give this query parameter a value.

So lets make a copy of this again

and go back.

I'll just clear the terminal and paste the query in again,

hit enter, and now everything is way easier to read.

Of course, this is not useful if we're working

within Kibana's console

but you might be in a scenario

where you're hacking away in a terminal

perhaps in an environment where

you don't have Kibana available.

Perhaps you're debulking a production cluster

from the command line or something like that.

Whatever the case, you now know

how to make the response pretty.

You should only do this for debulking

because its much more resource intensive,

but I guess that goes without saying.