---
id: 2h13fo7sb41catzgyhl65r9
title: 129_AddingSynonymsFromFile
desc: ''
updated: 1701195329784
created: 1701193340824
---
Instructor: You just saw how to define synonyms.

But what if you have lots of synonyms?

That quickly becomes inconvenient,

both because we cannot modify the synonyms

without deleting the index but also because we would end up

with a very large array within our index definition.

To solve those problems, you can define the synonyms

within a file, something that you will usually want to do.

We do that by specifying a parameter

named synonyms_path, containing the path to the file.

The file should be a text file containing the same format

as you saw in the previous lecture,

just with a rule per line and without quotation marks.

The path to the synonyms file should be either

an absolute path or relative to the config directory.

I have already added an example file in advance,

so let's take a moment to look at that.

Since I'm running my Elasticsearch cluster

within a Docker container, I need to run some commands

to get into the isolated container.

So the commands that you're about to see are Docker-specific

and if you haven't worked with Docker before,

then don't worry about it.

If you have just installed Elasticsearch directly

on your operating system, then you can just navigate

to the config directory within the command prompt

or terminal or within Explorer or Finder

or whatever your file explorer is named.

Anyways, I'll just enter a few commands

and navigate to the config directory.

Here I have created a directory named analysis,

in which I have a file named synonyms.txt.

Let's take a look at the contents of this file.

As you can see, the format is the same

as you saw in the previous lecture but instead of an array,

we just add one rule per line.

You can find this file within the GitHub repository

if you want to take a closer look at it or use it yourself.

All right, let's head back to Kibana.

You probably noticed that I modified the query

from the previous lecture in advance.

I have just specified a path

relative to the conflict directory.

Like I mentioned before, I could also have specified

an absolute path if I wanted to.

If you're on Windows, make sure that you specify a path

with back slashes instead of forward slashes.

Anyways, let's run the query but make sure

that you restart the node before doing so.

Also note that I have removed the existing index

from the previous lecture in advance,

in case you're following along.

Let's test that the analyzer works

in the same way as in the previous lecture,

just to show that the synonyms file is indeed being used.

So I'll use the Analyze API for the synonyms index,

specify our custom analyzer, my_analyzer,

and some text.

We'll write Elasticsearch.

The result is that the Elasticsearch term is replaced

with E-L-K, exactly as you saw in the previous lecture.

Okay, so everything works as before now.

But I just want to mention a few things

before ending this lecture.

First of all, the synonym files should be available

on all notes storing documents

for the index using the analyzer.

To keep things simple, the easiest way is probably to start

a file on all notes.

There is one more important thing that I want to mention.

If you add new synonyms to the file and restart nodes,

you might think that everything is good to go.

That might be the case but you might already have

index documents containing whichever synonym you are adding.

Let's take a look at an example

and see why that is problematic.

Suppose that we index a document

containing the term Elasticsearch

and then we add a synonym of E-L-K for that term afterwards.

We restart the appropriate nodes

and expect everything to be good.

But if we perform a search query for Elasticsearch,

it won't match anything.

The reason for that may be a bit tricky to understand

but at the time of indexing the document,

there is no synonym for the term Elasticsearch,

so the term is just added as is to the inverted index.

At a later point in time, a synonym is added.

That synonym is effective immediately

after restarting a node but only when indexing new documents

or updating existing ones.

That's because Elasticsearch picks up the new synonym

when restarting a node but it does not re-index documents

when starting back up.

Okay, so at this point the inverted index

contains the term Elasticsearch,

which is what we're searching for.

So why doesn't the document match?

Because the search query goes through

the same analysis process as the full text fields do.

A part of that process is to look for synonyms,

so when we run the search query,

Elasticsearch has picked up the new synonyms.

So instead of searching for Elasticsearch,

we are actually searching for E-L-K behind the scenes.

And since the inverted index

contains the term Elasticsearch,

the document does not match.

Hopefully you understand what the problem is.

So what is the solution?

It's actually something that you've seen before,

the Update By Query API.

Simply run the query that you saw earlier in the course

and you'll be all good.

After doing that, the query should match

because then the documents have been re-indexed.

And with that, enough about synonyms for now.

