---
id: ow1xbtdi031z3z7ma94x5ol
title: 18_CreatingAndDeletingMetrics
desc: ''
updated: 1701085556878
created: 1701080608343
---
Now that we have everything set up, it's almost time to add some documents to our cluster.

We will be dealing with how to manage data in this section of the course, but first I

want to show you how to create and delete indices.

You have already seen how to create an index, but this time I will configure the number

of shards and replica shards, just so you know how to do that as well.

We added an index named "pages" in the previous section, with the purpose of exploring

how shards are distributed across nodes.

We don't really need that index anymore, so let's go ahead and remove it.

Doing so is extremely easy.

Remember how Elasticsearch exposes a REST API?

This means that the HTTP verb is used as a way to specify which action we would like

to perform on some resource, in this case an index.

For delete operations, we simply enter "DELETE" as the HTTP verb, followed by the name of

the index.

Remember that a forward slash is optional with Kibana's Console tool, but I prefer

to include it.

That's it!

The "pages" index is now gone.

To replace it, I will add an index named "products." As you've seen before, we use the "PUT"

HTTP verb when adding an index.

Instead of using the default settings, I want to specify the number of shards and replica

shards for the index.

Note that I am just doing this for demonstration purposes.

For indices that you create for production purposes, you should stick to the default

values unless you have a reason for not doing so.

To specify index settings, we need to add a request body.

That's done by adding a JSON object on the next line.

The first line specifies the HTTP verb and endpoint, while the following lines specify

the JSON request body.

The index settings should be specified within a nested object named "settings," so let's

add this object.

Let's set both the number of shards and number of replica shards to two.

To specify the number of shards, we use the "number_of_shards" setting.

And to specify the number of replica shards, we use the "number_of_replica" setting.

That's it, so let's run the query.

The "acknowledged" key within the response indicates whether or not the index was successfully

created.

The "shards_acknowledged" key indicates whether or not the required number of shards

were started up before timing out.

By default, this refers to the primary shards.

That's it for creating and removing indices.

Let's move on to putting some documents into this new index.