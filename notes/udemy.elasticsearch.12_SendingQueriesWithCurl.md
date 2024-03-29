---
id: 5jehops8ea00zrpn9avj055
title: 12_SendingQueriesWithCurl
desc: ''
updated: 1701085858099
created: 1701085663311
---
You have just seen how to run queries using Kibana’s Console tool.

That’s the easiest way of running queries, because it helps us in a number of ways.

It formats the response so that it is easy to read, and it handles some things for us

under the hood, such as setting the correct Content-Type header.

Apart from that, it also provides auto-completion when typing our queries.

This is very convenient, because sometimes it can be hard to remember all of the different

queries that Elasticsearch provides.

These are the reasons why we will be using the Console tool throughout the course, but

I do want to show you how to run queries with cURL as well, just in case you prefer to do that.

You can use other HTTP clients as well, such as Postman.

You should already have cURL installed with the exception being for some old versions of Windows

I have attached a download link to this lecture in case you need it.

Anyway, let’s type out the simplest possible cURL command by simply specifying the endpoint

of our Elasticsearch cluster.

If you are using Elastic Cloud, be sure to use the Elasticsearch endpoint from the deployment

page and not the Kibana endpoint.

The GET HTTP verb is implicitly assumed if none is specific, but we can also specify

it with the -X argument as follows.

Let’s send the request.

As you can see, we get an empty response back from Elasticsearch.

That’s because from version 8 and onwards, we need to use the TLS endpoint instead of

plaintext, so let’s change that.

Now we get a certificate error.

The reason is that Elasticsearch generates a self signed certificate by default, which

is not trusted by HTTP clients for security reasons.

Note that this only applies to local setups, so if you created a cloud deployment, you

will not face this issue.

The easiest way to get around that is to simply use cURL’s --insecure flag as follows.

This flag instructs cURL to ignore the certificate error, and if you look closely, you can see

that we now get a different error.

This was an easy solution and it works just fine for local development, but the more correct

approach is to provide cURL with the CA certificate with the "cacert" argument.

Let me just type that out.

The CA certificate is located within the config/certs directory as you can see.

If your working directory is the Elasticsearch root directory, you can specify the relative

path just like I did.

Otherwise you can use an absolute path as well.

Running the command, you can see that the certificate error went away with this approach as well.

Alright, so far so good.

We still get an error, because we need to authenticate with our Elasticsearch cluster.

This also applies if you have created a cloud deployment instead of a local one.

Doing so is simple with cURL’s -u argument.

The value should simply be the username for your deployment.

For local deployments, the password is the one that was generated the first time Elasticsearch

started up.

When running the command, cURL will prompt us to enter our password.

Perfect, that worked as intended.

For the endpoint we defined, Elasticsearch returns basic information about our cluster.

As an alternative, you can supply your password for the -u argument as well.

Simply add a colon after the username followed by the password.

With this approach, cURL will not prompt us to enter the password when running the command.

The password will, however, be exposed within your terminal, so this is not ideal from a

security perspective - especially when communicating with a production cluster.

Anyway, that was the most basic request we could send.

Oftentimes we need to send some data along with our request, such as when searching for data.

Let’s update our request path to use Elasticsearch’s Search API for a products index.

This index doesn’t exist yet, but we will create it later.

The Search API requires us to send a JSON object specifying constraints for our query.

We will get back to searching for data later, so I will just use the simplest possible query

which matches all documents.

To specify the data, we can use cURL’s -d argument.

Let me just type it out.

Alright.

Don’t worry about the JSON object, but here is a formatted version of it anyway.

Notice how I enclosed it within single quotes to avoid having to escape all of the double

quotes with backslashes.

That approach doesn’t work on Windows because it doesn’t like single quotes.

Instead, you need to wrap the argument within double quotes and then escape each double

quote within the JSON object.

You can see an example on your screen, and you can copy it from within the GitHub repository

to save some typing.

Anyway, let’s hit Enter and see what we get.

We get an error back saying that the provided Content-Type header is not supported.

When adding data with the -d argument, cURL just assumes that we are mimicking a form submission.

Because Elasticsearch expects to receive JSON, we need to explicitly define which kind of

data we are sending.

That’s done by specifying a Content-Type header with a value of application/json.

That can be done with the -H argument as follows.

That should fix the error, so let’s send the request again.

Indeed the header error went away.

We now get a different error stating that the products index doesn’t exist.

That’s to be expected since we haven’t created it yet, so everything is good.

So that’s how to send requests to Elasticsearch with cURL.

If you encounter any problems, try checking the order of the arguments, as cURL is quite

sensitive in that regard.

Honestly, its behavior can seem a bit weird if you are not familiar with it.

If you prefer to use other HTTP clients, it should be fairly easy to replicate this in

Postman or something like that.

Alright, I’ll see you in the next lecture.

