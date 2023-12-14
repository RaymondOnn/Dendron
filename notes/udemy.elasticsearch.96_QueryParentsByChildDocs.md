---
id: 4d6nltv1u455pij17jrp110
title: 96_QueryParentsByChildDocs
desc: ''
updated: 1701194488618
created: 1701109972349
---
Instructor: Now that you have seen

how to retrieve child documents based

on parent document criteria,

let's now do the opposite.

Retrieve parent documents based

on criteria placed on their child documents.

Logically the query that enables us to

do this is named has underscore child.

Let's see it in action.

I'll begin by adding an empty object

for the has underscore child key

being the name of the query.

So has underscore child

and an empty object.

Within this object,

we first need to define the relation type

of the child documents for an option named type.

In this example,

the value is going to be employee

so let's type that out.

So type

and employee.

Next, let's add an empty query object

which is where we'll be defining the constraints

for the child documents

i.e. the employees.

Like so.

So what I want to do now

is to match employees

with an age greater than or equal to 50

and boost the relevant score if they're male.

There's no specific reason

for this particular query

other than to show you a slightly more advanced

query compared to the previous lecture.

So let's get started typing it out.

So that's a job for the bool query.

So let's add that

and first a must clause.

And in here I'm going to add a range query

which will take care of searching

for the age,

or rather constraining the age.

So the field is named age

and let's get rid of the LTE option

and just leave this one and set it to 50.

Alright,

let's now add

a should clause

with a term query.

I'll change the syntax here a little bit.

So the fields will be gender

and I'll use the keyword mapping

because it's a term level query

and I'll specify M short for male.

All right, that's it.

Let's go ahead and run the query.

Looking at the results,

we can see that the development

department matches

because it has an employee with an age of 52.

If I change the age to 60,

we should see that the department no longer matches.

So let's try that.

There we go.

And indeed the department no longer matches.

I'll just revert this change again.

Okay, so just to recap.

With the has underscore child query,

we are matching parent documents

that contain child documents matching some criteria.

This criteria can be any crew

that you have learned thus far

giving you maximum flexibility.

There are two more things that I want to mention

about the has underscore child query.

First off,

as with the has underscore parent query

we can make the query include the relevant scores

of matching child documents.

Unlike the has underscore parent query,

the option is named score underscore mode

and it's not just a bullion value

as its name implies.

There are five score modes that we can use.

So let's quickly go through each of them.

The min and max modes take the lowest

and highest score respectively

of any matching child document

and maps it into the relevant score

for the parent document.

The sum and AVG modes on the other hand,

include all of the child documents

as relevant scores

and calculate the sum and error respectively.

The resulting number is then aggregated

into the relevant score of the parent document.

Last but not least,

there is a score mode named none

which doesn't consider the relevant score

of child documents at all.

This is the default behavior

so you can either just set the value

to none explicitly

or simply leave it out entirely.

Let's quickly add the score underscore mode option

to our query just for the sake of an example.

I'll set the mode to sum.

So let's go up here

and add the option score underscore mode

and specify sum as the value.

And let's go ahead and run the query.

Notice how the relevant score

of the match document

is no longer just one.

The score just increased

by approximately a third

because now the sum of the matching child documents

scores have been aggregated into the score.

This approach is useful

when you don't just want to find

out which parent documents have child documents

matching the criteria,

but also want to take into account

how well the child documents match.

Naturally, the number of matching child documents

might also affect the relevant score

when using the sum scoring mode.

There are many scenarios where you can make use

of the score mode depending

on which kind of data you need to retrieve.

Next up, the has underscore child query

also allows us to

specify the minimum and maximum number

of children that must be matched

for the parent document to be included

within the search results.

We can do that by simply adding

the min underscore children

and max underscore children options.

So let's quickly do that.

So I'll set the min underscore children option

to two

and the max underscore children option to let's say five

and let's run the query and check the results.

Now we no longer get any matches.

That's because we set the minimum number

of children to two,

but the department that matched

before only has one child document

that matches the query

that we specified.

In this query,

we specified both of the options,

but you can of course leave out either one of them

if you want to.

Lastly, I want to quickly mention

that the has underscore child query

also supports sorting parent documents

by child documents in the same way

as the has underscore parent query supports sorting.

Since that requires scripting

I'm just going to attach a link to an example

to this lecture

because we haven't covered scripting yet

and that's it for this lecture.

I'll see you in the next one.