---
id: u1mflngroulfzwyw1drvme0
title: 74_RangeSearches
desc: ''
updated: 1701193994024
created: 1701105978932
---
You just saw how to search for one or more specific terms with the term and terms queries.

Sometimes you may want to search for values that are within a given range, such as products that

are almost out of stock. Or perhaps products that we created sometime between two dates.

Regardless of whether we want to search for values within a numeric or date range,

that’s done with the range query. Let’s take a closer look.

Just to save you from watching me type, I have prepared a couple of

queries in advance. If you want to run them and inspect the results,

they are of course available for you to copy within the GitHub repository.

The overall structure of the range query resembles that of any other query.

We add a key with the name of the field that we want to query and an object as its value.

This object then contains the parameters for the query - in this case the gte and lte parameters.

That’s short for “greater than or equal to” and “less than or equal to,” respectively.

This query therefore matches products

where the in_stock field has a value between one and five, both included.

You can see the SQL equivalent of this query at the bottom of the diagram, just for reference.

If we don’t want the boundaries to be included,

we can simply remove the letter “e” from the parameter names as follows.

You can mix these four parameters however you want. Here is a quick

overview of the parameters along with their SQL and math equivalents.

Feel free to pause the video if you want to take a closer look.

Apart from querying numeric ranges, we can specify dates instead. Elasticsearch

will automatically understand how to handle this whenever we specify a field of the date datatype.

In this example I only specified the date part and left out the time.

In this case Elasticsearch will use sensible defaults automatically.

In this case that would be midnight and one second before midnight, respectively.

When possible, I do recommend specifying this explicitly,

but that’s just a matter of preference.

By default, dates should be specified in the format that corresponds to the field’s mapping.

That’s typically the default format that we discussed earlier,

which is also the case for our date field in this example.

If for whatever reason you need to specify dates in a different format,

you can do so by specifying it within a parameter named format.

Consider the following query which specifies dates in a different format,

being the day, month, and then year.

The result of running this query is the same as for the one you saw a moment ago.

It’s also possible to specify a UTC offset by using a parameter named time_zone.

Remember that dates in Elasticsearch are stored as UTC dates, so if no time_zone

parameter is specified, the provided dates are assumed to be in the UTC time zone as well.

By specifying an offset, we can supply dates in a different time zone.

When processing the query, Elasticsearch will then convert the dates to the UTC

time zone before comparing them with the stored dates.

In this example, we specified an offset of one hour compared to the UTC timezone.

When converting the dates to UTC, one hour is therefore subtracted from the

specified dates before comparing them to the stored UTC dates.

This approach is useful if you want to let Elasticsearch handle timezones instead of

doing it at the application level. Alternatively you can convert any local dates to the UTC time

zone at the application level and then just supply these without the time_zone parameter.

And that’s how to perform range searches in Elasticsearch.