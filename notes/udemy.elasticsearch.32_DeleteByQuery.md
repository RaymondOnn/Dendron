---
id: kzecenh6pw6heb1bwz4aohi
title: 32_DeleteByQuery
desc: ''
updated: 1701104231313
created: 1701080906907
---
A bit earlier in this section, you saw how to delete a single document based on its ID.

Let's now look at a slightly more complicated way to delete documents; based on a condition.

You saw this in the context of updating documents in the previous lecture, so this is all going

to be very familiar to you.

Instead of updating documents that match a given search query, we are going to delete them.

Perhaps unsurprisingly, the API is named Delete By Query.

To keep things simple, I will make use of the "match_all" query that you saw earlier,

so let's type out the query.

This query will delete all documents that match

the query, which is all documents within the "products" index in this case.

Let's run the query.

Within the results, we can see that two documents were deleted, which is what we expected.

That's really all there is to it.

The Delete By Query works in exactly the same way as the Update By Query.

This means that it also handles errors the same way, uses batches, primary terms and

sequence numbers, etc.

Basically everything we discussed in the previous lecture.

Therefore you may also add the "conflicts" parameter with a value of "proceed" if

you wish to ignore version conflicts, in exactly the same way as you saw in the previous lecture.

And that's how to delete documents that match a given search query.