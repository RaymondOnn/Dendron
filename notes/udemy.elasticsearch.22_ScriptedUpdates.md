---
id: b0z44s2awauyh07aazygz2x
title: 22_ScriptedUpdates
desc: ''
updated: 1701103986422
created: 1701080701823
---
In the previous lecture, we reduced the "in_stock" field by one, which we did by explicitly specifying

its new value.

Wouldn't it be cool if we could subtract the value by one, without needing to know

the current value for the document?

That is, if we could do everything in one go, instead of having to retrieve the document

first, subtract the field value by one, and then update the document.

We can do exactly this with so-called "scripted updates." Elasticsearch supports scripting,

which enables you to write custom logic while accessing a document's values.

In this example, we can easily increase or decrease the "in_stock" field's value.

That's probably the simplest possible example, so that's the one I will go with, because

scripting is a relatively big topic that we will cover later in the course.

I do want to mention, however, that you can also embed "if" statements and such, so

you can think of scripting as a way to write custom code that looks similar to many programming

languages.

To use a script, we will actually use the Update API as we did in the previous lecture,

but just specify a "script" object within the request body.

Within this object, we should add a "source" key and provide our script as its value.

I will just write out the script and explain it afterwards.

"ctx" is a variable, and is short for "context." We can access the source document

through its "_source" property, which gives us an object containing the document's fields.

In this case we accessed the "in_stock" field and subtracted one from it.

If we wanted to increase the value instead, we would simply write "++" at the end.

In this example I added the script on a single line, which is ideal for very simple scripts.

If you have more advanced scripts, however, you can use three double quotes at the beginning

and end of the script instead of one.

This marks the string as a multiline string, allowing your script to span multiple lines.

You will see this at the end of this lecture.

For now, let's go ahead and run the query that we just wrote.

If we retrieve the document, we should see that the "in_stock" field equals two instead

of three.

We can also do assignments instead, so let's assign a value of ten to the field.

The value should now be set to ten, so let's check.

And indeed it is.

We can also define parameters within the request.

Of course this is not very useful when we define the query within the Console tool.

However, if we were sending the query from an application of some sort, then we could

make the parameter dynamic based on some logic.

For example, if someone purchases four toasters, we could reduce the value of the "in_stock"

field by four instead of just one.

Let's do just that and pass in a parameter and give it a value of four.

We define parameters within an option named "params," which is an object consisting

of key-value pairs, where each pair corresponds to a parameter name and value.

With the parameter in place, let's modify our script to make use of it.

Let's run the query and verify that the field value is reduced by four, meaning that

it should be set to six.

As you can see, the result is what we expected.

Before ending this lecture, I want to mention two other possible values for the "result"

key that is specified within the query results, apart from "updated." The key may also

contain the value "noop." In the previous lecture, I told you that this is the case

if we try to update a field value with its existing value.

The same is not the case with scripted updates; if we set a field value within a script, the

result will always equal "updated," even if no field values actually changed.

There are two exceptions to this, both being if we explicitly set the operation within

the script.

For example, we can write a script that ignores a given document based on a condition.

This is done by setting the "op" property on the "ctx" variable to "noop."

You can see an example of such a script on your screen.

What this script does, is to only reduce the value of the "in_stock" field by one if

it has a value other than zero.

If the value is zero, no changes are applied to the document, and the "result" key

will be set to "noop" within the response.

Otherwise it will be set to "updated."

Note that you can write a similar script that just inverts the conditional statement and

only reduces the field value if it evaluates to "true."

The difference between these two scripts, is that the one to the right always yields

a result of "updated," regardless of whether or not the field value was actually changed.

This is not the case for the one on the left.

So if it's important for you to detect if nothing was changed, then that's the way

to go.

Actually, you can also set the operation to "delete," which will cause the document

to be deleted instead.

This will set the value of the "result" key to "deleted" within the results.

This is rarely useful though, as you can also delete documents that match a given query,

as you will see later in this section, so this should only be used for situations where

you need to use scripting to determine if a document should be deleted.

You can see an example of such a query on your screen.

The query decreases the value of the "in_stock" field by one as long as it is less than or

equal to one.

This effectively means that the product will be removed whenever the last product is sold.

That's just one example of how this functionality can be used.

If you want to try out these queries, you can find them within the GitHub repository.

That's how you can update a document with a script.