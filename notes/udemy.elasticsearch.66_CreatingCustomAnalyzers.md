---
id: yambv9muyx9jgj9qg9ode0a
title: 66_CreatingCustomAnalyzers
desc: ''
updated: 1701193798601
created: 1701105386433
---
Now that you know how to use the built-in analyzers in your mapping, let’s try to

create a custom analyzer.

We will create a new throwaway index at the same time.

First we need to add an object named “analysis,” nested within a “settings” object.

Analyzers must be declared within an object named “analyzer,” so let’s add that object.

We then specify the name of our analyzer as a key with an object as its value.

I will just name it “my_custom_analyzer” in lack of a better name.

Now comes the more interesting part; configuring the analyzer.

So what do we want it to do?

Consider the text that I have prepared for the Analyze API.

It contains some HTML markup and a couple of HTML entities.

Typically you will want to strip this out before indexing values into Elasticsearch,

but suppose that this hasn’t been done.

Analyzing this text using the “standard” analyzer is going to yield some strange terms.

Let’s check what the query outputs.

As you can see, the letters within the HTML entities become terms, and so do the HTML

tags, excluding the symbols.

That’s of course not ideal, because we don’t want search queries to match any HTML.

Fortunately there is a character filter that does two things; it strips out HTML tags,

and it converts HTML entities to their decoded values.

In this example, we will see the HTML entity for an apostrophe being converted to an actual apostrophe.

Let’s modify the query to make use of this character filter, which is named “html_strip,” by the way.

Let’s first get rid of the “standard” analyzer so that we can see exactly what the

character filter does on its own.

Now we can simply add a parameter named “char_filter” containing an array of strings, being names

of character filters.

Let’s run the query and check the difference.

We haven’t specified a tokenizer, so the input text is just emitted as a single term,

but that’s okay for now.

The HTML tags have now been removed, and the HTML entities have been converted to an apostrophe

and whitespaces.

This is exactly what we were looking to accomplish, so let’s add the character filter to our

custom analyzer.

First, we need to specify the type of the analyzer.

We did that in the previous lecture where we configured a built-in analyzer.

That involved specifying the name of an existing analyzer, but that’s not what we need to do this time.

Instead, we need to set the type to “custom” to tell Elasticsearch that we are building

our own analyzer.

Then we simply specify the name of the character filter within a parameter named “char_filter,”

so exactly what we just did with the Analyze API.

So far so good!

We still need to specify a tokenizer at the very least, so let’s just go ahead and use

the “standard” tokenizer.

To save a bit of time, I am not going to test the following steps with the Analyze API,

but I will test the completed analyzer to make sure it works as expected.

You can find the full query for the Analyze API within the GitHub repository in case you

want to play around with it and see how each part of the analyzer affects the output.

Let’s add the tokenizer to our custom analyzer.

Next up, we have token filters.

You will almost always want to lowercase letters when dealing with “text” fields, so let’s

add the “lowercase” token filter to the analyzer.

Token filters are specified as an array for the “filter” parameter.

Let’s also remove stop words, which can be done by adding the “stop” token filter.

The “stop” token filter removes stop words for the English language by default.

That’s good enough for now, but I will show you how to configure that in a moment.

Let’s add a third token filter, just for the sake of it.

Notice how the word “açaí” contains two special characters.

I am admittedly not sure what the correct definition is, but notice how the letters

“c” and “i” are special.

We probably don’t want to require our users to enter the letters in the same form, because

most probably won’t.

What we can do instead, is to convert the letters to their ASCII equivalents with the

“asciifolding” token filter.

Let’s add it and you will see the difference in just a moment.

That’s it for our fairly basic analyzer.

Time to create the index and test the analyzer.

You can make use of the analyzer in exactly the same way as you saw in the previous lecture.

I will just test it with the Analyze API.

Since the analyzer belongs to the “analyzer_test” index specifically, we need to specify the

index name within the endpoint.

This enables us to set the analyzer to “my_custom_analyzer.”

Looking at the result, we can see that the input has been tokenized, and that the terms are lowercased.

We can also see that a couple of words are missing, being stop words.

Lastly, the word “açaí” has been converted to plain letters.

That’s surely not the correct terminology, but I’m sure you see the difference.

Those are the basics of how to create custom analyzers.

However, sometimes you might need or want to configure parts of an analyzer.

For instance, we might need to index text in a language other than English, which would

require us to change the language of the “stop” token filter.

The approach of doing that is actually the same as when configuring a built-in analyzer

as you saw in the previous lecture.

Let’s make a copy of our query and modify the analyzer to remove Danish stop words instead.

Token filters are defined within a “filter” object, nested within the “analysis” object,

so let’s add that.

We then add a key with the name of the token filter, and an object containing its configuration.

This configuration essentially just takes the default configuration for the “stop”

token filter and overrides the “stopwords” parameter, instructing the filter to remove

Danish stop words instead of English ones.

We can now make use of this token filter within our custom analyzer.

That was pretty easy, right?

I am not going to run the query because the index already exists, but you can simply change

the index name if you want to run it.

We can do the exact same thing for character filters and tokenizers, within the “char_filter”

and “tokenizer” keys, respectively.

I will just add those as empty objects for your reference.

I haven’t really given you an overview of which character filters, tokenizers, and token

filters are available.

There’s a good reason for that; there are a lot.

Especially token filters.

We will get back to some of them later in the course such as one for handling synonyms.

If you have the time, then I encourage you to check out the list at the link that I have

attached to this lecture.

Otherwise just check out the documentation if you ever need to create a custom analyzer,

and you should be absolutely fine.

Anyway, that’s how to create custom analyzers.