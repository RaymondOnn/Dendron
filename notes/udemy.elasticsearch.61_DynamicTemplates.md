---
id: jgyltvfrzoftsuhc45ocf6e
title: 61_DynamicTemplates
desc: ''
updated: 1701193693278
created: 1701105274507
---
Another way in which we can configure dynamic mapping is by using so-called dynamic templates.

A dynamic template consists of one or more conditions along with the mapping a field

should use if it matches the conditions.

Dynamic templates are used when dynamic mapping is enabled and a new field is encountered

without any existing mapping.

Let’s get straight to business so you can see what I mean.

I have a partial query prepared, which will add a new throwaway index.

Dynamic templates are added within a key named “dynamic_templates,” nested within the “mappings” key.

Each dynamic template should be an object within this array, so let’s add an empty object.

Within this object we add a key with the name of the dynamic template, which can be anything we want.

Its value should be an object.

I will name the template “integers.”

You will see why in a moment.

The first thing we need to do is to define when this template should kick in, i.e. a match condition.

As you will see in a moment, there are several ways of doing this, but let’s begin with

the simplest one; matching based on the detected data type.

I will quickly type that out and explain it afterwards.

We are not done yet, but let’s take a moment to talk about this parameter.

The “match_mapping_type” parameter is used to match a given JSON data type.

On your screen you can see a table listing JSON data types and which value should be

used for the parameter within the dynamic template.

It’s important to note that the values to the right are not the data types that fields

will be mapped as; we will define those in a moment.

It’s also worth noting that “double” is used for numbers with a decimal, since

there is no way of distinguishing a “double” from a “float” in JSON.

Likewise, we also cannot distinguish between an “integer” and a “long.”

As a result, the wider data type will always be chosen.

If we go back to the query that we are working on, we set the match type to “long,” meaning

that the template will be applied to whole numbers.

We still haven’t defined what should happen if the condition matches, so let’s do that now.

What we have to do is to define a field mapping that should be used if our condition matches.

This is done within a “mapping” parameter, which should be in the same format as the

field mappings you have previously seen.

This means that we can define the data type that should be used, so let’s do that.

That’s the most basic structure of a dynamic template.

We defined a condition that evaluates to true if the detected JSON data type is “long,”

in which case the field mapping will be as specified.

In this example, this means that dynamic field mappings will not use the “long” data

type as would otherwise be the default behavior, but the “integer” data type instead.

If we wanted to, we could add mapping parameters to the field mapping as well, but let’s

keep things simple for now.

Let’s run the query to create the index.

Let’s now index a simple document with a field that matches the condition that we defined.

By default, this new field would be mapped as a “long” field, so if everything works

as intended, we should see it being mapped as an “integer” instead.

Let’s inspect the mapping and take a look.

Indeed we can see that the field was mapped as an “integer,” so the dynamic template

worked as intended.

If you don’t want to just take my word for it, you can of course test the difference

with and without the template.

Another example of a dynamic template could be to adjust how strings are mapped.

As you know, they are mapped as both a “text” mapping and a “keyword” mapping by default.

That might be what you want, but it could also be the case that you only want one of them.

Or perhaps you want to change the value of the “ignore_above” parameter or get rid

of it altogether.

Whatever the case, that would be a good use of a dynamic template.

The example you see on your screen now shows how string values can be mapped according

to the default rules, with the exception that the “ignore_above” parameter now contains

a value of 512 instead of 256.

That’s probably of limited use, but hopefully you get the point of how the default mapping

rules can be tweaked to fit your needs.

Let’s now move on to slightly more advanced dynamic templates.

I don’t want to spend a lot of time typing out the queries, so I will just show them

on some slides and explain what they do.

As always, you can find the queries within the GitHub repository if you want to run them.

Besides simply matching based on the detected JSON data type, we have a number of other

options available, so let’s go through them one by one.

First up, we have the “match” and “unmatch” parameters.

These parameters enable us to specify conditions for the name of the field being evaluated.

The “match” parameter is used to specify a pattern that the field name must match.

The “unmatch” parameter can then be used to specify a pattern that excludes certain

fields that are matched by the “match” parameter.

I hope that makes sense.

Otherwise you will see an example in a moment.

Notice how I mentioned the word “pattern” a couple of times for both of the parameters.

That’s because both of them support wildcards with the asterisk symbol.

This makes sense, because hard coding specific field names into dynamic templates probably

doesn’t make much sense.

Let’s take a look at an example of how these parameters can be used.

The first thing to notice is how this example actually includes two dynamic templates.

In situations where there are more than one, the templates are processed in order, and

the first matching template wins.

The first template matches fields where the name begins with the string “text” and an underscore.

The “unmatch” parameter then filters out fields that end with an underscore and the string “keyword.”

What this template does is therefore to map string values to “text” fields — provided

that the field name matches the pattern defined by the “match” parameter and doesn’t

match the pattern defined by the “unmatch” parameter.

Fields names that end with an underscore followed by the string “keyword” are caught by

the second template that maps them as “keyword” fields.

This means that if we run this query… we will get this mapping.

The description field matched the first template, whereas the product ID field matched the second.

Did you notice how both of the templates included the “match_mapping_type” parameter as well?

That’s because we can include more than one condition within a dynamic template; the

parameters that you have seen — and will see — can all be combined to form a set

of match conditions, so you are not limited to just one.

Using the “match” and “unmatch” parameters is useful for using naming conventions as

a way of applying field mappings.

If we need more flexibility than what the “match” parameter provides with wildcards,

we can set a parameter named “match_pattern” to “regex.”

What this does, is to adjust the behavior of the “match” parameter to support regular expressions.

In this case the regular expression matches strings that end with “_name.”

Adding a document with these three field names... will therefore create the following mapping.

This approach is useful in situations where simple wildcards are not enough and you need

the flexibility of regular expressions.

Very similar to the “match” and “unmatch” parameters, we have the “path_match” and

“path_unmatch” parameters.

The difference is that these parameters match the full field path, instead of just the field name itself.

To be clear, by “full path” I am referring to the dotted path.

For instance, you might have a “name” object containing a “first_name” key,

in which case the path would be “name.first_name.”

We can also use wildcards within these paths.

Let’s look at an example.

Here we use the “path_match” parameter to match any name part.

The “name” object will contain three keys; “first_name,” “middle_name,” and “last_name.”

The wildcard matches all of these.

I haven’t added the “path_unmatch” parameter, but it behaves the same as the “unmatch”

parameter, except that it considers the field path.

What I have added, though, is the “copy_to” mapping parameter, which copies values into

the specified field.

In this case it enables us to query the full name as a single field, instead of having

to query three different fields.

Adding the following document... will therefore cause the following mapping to be created.

A mapping will also be created for the “full_name” field, but I have omitted it from this diagram.

It will be created using the default dynamic mapping rule for string values, meaning that

it will be mapped as both a “text” and “keyword” field.

Just one thing left to cover before we are done.

Within the “mapping” key of a dynamic template, you can make use of two placeholders.

The first one is named “dynamic_type,” which you can see in use on your screen.

We can use placeholders within string values by enclosing the placeholder name within curly brackets.

The “dynamic_type” placeholder is replaced with the data type that was detected by dynamic mapping.

This template matches all data types and adds a mapping of that same data type.

If we didn’t have this placeholder at our disposal, we would have to add a dynamic template

for each data type, which would be quite inconvenient.

The purpose of this template is to set the “index” parameter to “false.”

The data type is going to be the same as it otherwise would be with dynamic mapping.

This particular example with disabling indexing could be used for time series data.

Perhaps you recall me mentioning that this is a common optimization for time series data

where you typically don’t need to filter on specific values, but rather aggregate by

time intervals.

Another optimization for time series data is to disable norms, which you can do in exactly

the same way.

Anyway, let’s check the mapping that is created when adding the following document.

As you can see, both field mappings have the “index” parameter set to “false” as we expected.

Before ending this lecture, I want to briefly talk about the differences between dynamic

templates and index templates, just in case you are confused.

An index template applies field mappings and/or index settings when its pattern matches the

name of an index.

A dynamic template, on the other hand, is applied when a new field is encountered and

dynamic mapping is enabled.

If the template’s conditions match, the field mapping is added for the new field.

A dynamic template is therefore a way to dynamically add mappings for fields that match certain

criteria, where an index template adds a fixed set of field mappings.