---
id: 1gvf2hhnaihoqwvtu481p9n
title: 101_JoinFieldPerformanceConsiderations
desc: ''
updated: 1701194608881
created: 1701110126955
---
Now that you know how to use joining queries and how to map document relationships, let’s

talk a bit about the performance of these queries.

Note that in this lecture, I am talking specifically about the performance of the “join” field,

i.e. parent/child joins, and not the “nested” query.

Well, let me just begin by summarizing the takeaway from this lecture; join queries are

expensive in terms of performance and should be avoided whenever possible.

There are a few cases where using join fields makes sense, but in general they should be

used with caution.

Of course this is a slight simplification, as it also depends how much data you are dealing with.

If you don’t care for the details of this, then you are welcome to skip the remainder

of this lecture.

Otherwise, let’s dive into a bit more detail.

Like I said, using “join” fields is slow.

For example, the performance of the “has_child” query degrades the more child documents that

point to unique parent documents.

Essentially the query just gets slower the more documents you add to an index that uses

a “join” field.

You won’t notice this in the beginning, but as the number of documents increases,

you will start to see increasing query times, which will eventually become quite significant.

The same applies for the “has_parent” query, where the number of parent documents

affects performance negatively.

Apart from that, if you make use of multi-level relations, then each level of relation adds

an overhead to your queries, so you should almost always avoid this.

That being said, there is a scenario where it makes sense to use a “join” field and

where the performance will still be good.

That’s if you have a one-to-many relationship between two document types and there are significantly

more of one type than the other.

Having recipes as the parent documents and ingredients as child documents, is a good

candidate for using a “join” field, because there are going to be significantly more ingredients

than recipes.

You could also map the ingredients as nested objects if you wanted to, but the point is

that it would also be fine to use a “join” field in this case.

Alright, so now that you know a bit more about the implications of using “join” fields,

there are two more questions left to be answered.

The first one is: if using “join” fields is usually discouraged, then why on Earth

did I just spend so much time talking about it?

Well, because there are still times where it’s perfectly fine to make use of parent/child

relations, such as in the example that I told you about a moment ago.

And also if you have limited number of documents and don’t anticipate that it’s going to change much.

Secondly, it’s still a part of Elasticsearch, and it’s not my job to decide how you are

going to map your documents.

That’s also impossible, as every use case is different.

It is, however, my responsibility to give you the tools that you need to make a qualified

decision and my recommendations, but in the end the choice is yours.

The other question is: if “join” fields are usually not a good idea to use, then how

do we map document relationships properly?

Well, for one, we have the “nested” data type, but that might not always suit your needs.

So generally speaking, you shouldn’t map document relationships.

Elasticsearch is very efficient for searching through tons of data, but Elasticsearch is

a very different technology than a relational database, for instance.

This means that you should store data as a structure that is optimized for efficient searching.

I mentioned this in one of the first lectures of the course, but now seems like an appropriate

time to recap on that.

Suppose that you want to map employees as documents.

Instead of having the employee’s address as a stand-alone document, you can either

map it as a nested object, or you can just add the fields like any other fields.

The point is that you denormalize the data instead of trying to map document relationships,

because that’s not how things are supposed to be done in Elasticsearch, at least generally speaking.

So unless you have a good reason for using “join” fields, try to just denormalize

your data and use “join” fields whenever that doesn’t work for you, and you can accept

the consequences and limitations of using “join” fields.