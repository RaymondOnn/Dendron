---
id: obb3xax9wnifpg6jgactzh9
title: 47_DotNotationInFieldNames
desc: ''
updated: 1701104693774
created: 1701085247539
---
When we added the mapping for the “reviews” index, we mapped an “author” field as an object.

In Elasticsearch, objects are mapped implicitly by using the “properties” mapping parameter

at each level of the hierarchy.

You can see the query that we ran at the top of the Console tool.

Since the “author” field doesn’t contain any nested objects, the mapping doesn’t

look too bad, but for more advanced mappings, it can look a little bit messy.

Instead of defining objects in this way, we can use an alternative approach; by using

a dot notation.

The way it works is that we add a dot at each level of the hierarchy, i.e. as a field separator.

In this case, we can write “author.first_name,” for instance.

This format is a bit easier on the eyes, particularly if you intend to nest objects further than

just one level.

I have rewritten the query to use this syntax in advance and used a new index name to avoid

a conflict with the existing one.

Let’s retrieve the mapping for the newly created index.

As we can see, the mapping looks exactly the same as it did with our previous approach.

This is because Elasticsearch translates fields using a dot notation into using the “properties”

parameter behind the scenes.

You will therefore never see an index’ mapping contain the dot notation, as this is simply

a shortcut while creating the mapping.

Note that this dot notation is not exclusive to defining field mappings; it can also be

used within search queries.

Anyway, I just wanted to show you this handy little trick, so that’s all for this lecture.