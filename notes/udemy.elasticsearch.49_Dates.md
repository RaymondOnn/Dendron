---
id: 7evnrvxryz7kp7bvbg2fs86
title: 49_Dates
desc: ''
updated: 1701104742043
created: 1701085296269
---
We just added a “date” field mapping.

Before indexing a document with this new field, let’s talk a bit about how Elasticsearch handles dates.

Dates may be specified in one of three ways; specially formatted strings, a long number

representing the number of milliseconds since the epoch, or an integer representing the

number of seconds since the epoch.

The latter is also referred to as a UNIX timestamp.

As you might know, epoch refers to the 1st of January 1970.

It is possible to specify custom date formats, but we will look at that a bit later in this section.

Instead, let’s take a look at how “date” fields behave by default, i.e. when no custom

format is specified.

Elasticsearch expects one of three formats; a date without time, a date with time, or

a long representing the number of milliseconds since the epoch.

In the case of string timestamps, these are assumed to be in the UTC timezone if none

is specified.

If you supply a string value, the date needs to be in the ISO 8601 format.

This is the most standard date format and what is generally used besides numeric timestamps.

In case you are not familiar with the format, I will show you a couple of examples in a moment.

I have attached a link to a page that explains the format, so I encourage you to check that

out if you are not familiar with it.

Alright, so those were the basics of how dates should be specified within documents - at

least when using the default date format.

Let’s talk a bit about how dates are stored within Elasticsearch.

Dates are stored as a long number representing the number of milliseconds since the epoch.

Even if you supply a string date, Elasticsearch will parse it and convert it to a long number for storage.

You can see that on the diagram, where a document is indexed.

The value of the “created_at” field is converted into a long number, which is the

value that is stored.

If a date includes a timezone, Elasticsearch will convert the date to UTC first.

All queries are then run against this long number, giving Elasticsearch a uniform way

of working with dates, regardless of which date formats are used.

The date conversion will also happen for search queries; suppose that we send a query to match

reviews written after a given date and we supply the date as a string.

This date will then be converted to a long number before the search process begins.

Alright, now that you know the basics of how dates are handled, let’s index a couple

of documents.

I have prepared the queries in advance to save some typing, so let’s go through them

one by one, as each of them uses a different date format.

The first query only specifies a date, meaning that the time of day has been left out.

Typically you will include the time as well, but you don’t have to if you don’t need it.

An example could be to store the date of birth of a person.

When only a date is specified, Elasticsearch will just assume the time to be at midnight

and then convert the date to the number of milliseconds since the epoch.

The second query does include the time of day as well.

Notice how the date and time is separated by the letter “T” in order to be compatible

with the ISO 8601 specification.

It also ends with the letter “Z.”

So what does that mean?

That’s the timezone part of the date.

“Z” simply means that the date is in the UTC timezone.

This wasn’t specified explicitly in the first query, because no time was provided,

and so the date was assumed to be in the UTC timezone.

But what if the date is not in UTC?

In that case the UTC offset can be specified instead of “Z.”

In this example I have specified that the timestamp is offset by one hour compared to UTC.

Elasticsearch will then take this into account when converting the timestamp to a long value.

The last option is to specify a long number representing the number of milliseconds since the epoch.

It’s really important that you don’t specify a UNIX timestamp here, i.e. the number of

seconds since the epoch.

If you do that, Elasticsearch won’t give you an error because it will just treat the

number as the number of milliseconds since the epoch.

You might then think that everything is okay, since the documents are indexed correctly.

However, when you search for documents within a given date range, you won’t get any matches,

because the dates are actually way in the past.

If you do have a UNIX timestamp, then be sure to multiply that number by 1,000.

Anyway, let’s index this document as well.

I also have the simplest possible search query prepared, so let’s run that one as well.

Keep an eye on the “created_at” field as I scroll through the results.

The values are the exact values that we specified when indexing the documents, and they do not

represent how dates are stored for searching.

You also saw this in the context of type coercion; the concept is the same, so just consider

this a small reminder.

Those were the basics of how dates are handled in Elasticsearch.

Later in the course, you will learn how to use dates in search queries, and how to work

with relative dates and date math.