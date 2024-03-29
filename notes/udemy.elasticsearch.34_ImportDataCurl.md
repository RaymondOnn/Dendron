---
id: d7eh7to4f530bnhhoijl12v
title: 34_ImportDataCurl
desc: ''
updated: 1701104382890
created: 1701080958285
---
Now that you know how to use the Bulk API, let's use it from the command line to import

some test data that we will be using throughout the course.

To do this, I will use an HTTP client called cURL, which is the most popular command-line

tool for sending HTTP requests.

If you are using macOS or Linux, you should already have it installed.

The same actually applies to some Windows installations, so you might not have to install

it yourself.

If it's not available on your system, I have attached a link to the download page

to this lecture for your convenience.

Note that you can use any other HTTP client as well if you prefer, but I won't cover

that in this lecture.

The first thing you need to do is to download the file containing the test data.

You can find the file attached to this lecture or within the GitHub repository.

I have the file opened up in a text editor, just to give you an idea of what the file contains.

As you can see, the file just contains a lot of "index" actions; 1000 to be exact.

Each document has been assigned a sequential ID.

Since the index name is not specified anywhere within the action metadata, this tells us

that we need to specify the index name in the request itself.

More precisely, within the request path.

Before importing the data, I want to scroll down to the bottom of the file.

As I mentioned in the previous lecture, the file needs to end with a newline character.

In a text editor, this just means that the last line of the file should be empty.

I have already done this for the files that I have uploaded, so the import should work

out for you.

However, I still see some people getting an error stating that the file needs to end with

a newline character.

If that is the case for you, you just need to open up the file in a text editor and ensure

that there is an empty line at the end of the file.

With that out of the way, let's type out the command that sends an HTTP request to

the Bulk API.

For that, we need to open a terminal window.

The working directory should be the directory in which the downloaded file is placed, which

is the desktop in my case.

In case you are new to the terminal, you should use the "cd" command for this, and you

can find the necessary commands within the GitHub repository.

Alright, so naturally the command begins with "curl."

Following that, we need to specify the "Content-Type" header, which we can do with the -H option.

This header needs to specify the NDJSON content type as discussed in the previous lecture.

Then we need to specify the HTTP verb.

If we leave it out, it defaults to "GET," but we need it to be "POST." We use the

-X option to specify the verb.

If you are using Elastic Cloud, then remember to add the "u" option here as you saw

in the previous section of the course.

Following the HTTP verb, we enter the URL to the Bulk API.

Since we are no longer using the Console tool, we of course need to specify the fully qualified URL.

Since I am working with a local Elasticsearch cluster, that will be localhost on port 9200.

If you want the results to be formatted nicely for your eyes, you can add a "pretty"

query parameter without any value.

Since the Bulk API is almost exclusively used within some kind of script, the results will

also typically be processed by a script, so that is probably of limited use.

However, if you want to inspect the response for testing purposes, you can go ahead and

add that query parameter.

Finally, we need to actually send some data along with the POST request.

We do that with the "data-binary" option.

Notice that this option must be prepended with two hyphens instead of just one.

We then add the file name within double quotes, prepended with an "at" (@) symbol.

This symbol tells cURL that the value is a file name and not a path.

Let's type that out.

We send the file content along as binary data, because we want to preserve the newline characters.

Other cURL options process the data, causing newlines to be removed.

That's it!

Let's run the command.

After a short moment, our Elasticsearch cluster responds with a result set of the same format

as you saw in the previous lecture.

If you are sending bulk requests within a script, you would inspect the results at this

point, but in this case we are happy with this.

In case you were wondering why we were allowed to run this command when our index already

contained a couple of documents...

That's because the bulk request contains actions with the same document IDs.

And since the actions are all set to "index," these existing documents are simply replaced,

so that's why we didn't need to delete them first.

Had we used the "create" action instead, we would see that a couple of actions would

have failed.

Let's quickly inspect the shards within our cluster to see how the documents have

been distributed.

I have already prepared the query, because you have seen it before, so let's just go

ahead and run it.

As you can see within the results, the documents that we just indexed, have been quite evenly

distributed across the two shards that the index consists of.

The distribution is not perfect, but the default routing strategy does a pretty good job of

evenly distributing the documents.

Note that it might take a couple of minutes before the number of documents are updated.

So if the document count is not one thousand within the query results, you need to wait

a couple of minutes before running the query again.

That's it - we now have some data to work with.