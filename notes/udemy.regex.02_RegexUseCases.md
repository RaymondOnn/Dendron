---
id: jd79di39qwafgn1nt2dqpbo
title: 02_RegexUseCases
desc: ''
updated: 1700055687790
created: 1700055641046
---
Lets go through a couple of use cases of regular expressions in this session.

This is the first use case.

Here is a web page.

Those of you, who use Gmail should be very familiar with this page.

For all the Google haters, this is the sign-up page for getting a new Google Account.

It asks you for a first name, last name, preferred user id, and a password.

Lets start filling it up with some data.

I'll just make up something.

First name, Bob.

Last name, Marley.

For username, bob-the-greatest-marley

Next is the password field. Note that there is an instruction text right below the password field that reads:

"Use 8 or more characters with a mix of letters, numbers and symbols"

I hate to follow instructions, so I'll type in a password ...

...which is only 5 letters.

I'll use a-l-p-h-a alpha as my password.

Let me click this view icon, so that the password is visible.

There you go.

Now, click Next.No messing around with Google.Fine, I'll make my password alphabeta, so it has more than 8 letters now.

Nope.

Ok I'll add in some numerals. alphabeta123.

Nope. Ok let me pop in a semicolon at the end.

Yep, that worked.

When we typed in password, that did NOT meet Google's requirements, somebody or something was validating what we typed in

, each time...For this validation, a regular expression is used.

So there is a predefined pattern for a valid email address,

which is represented by a regular expression.

It might look something like this.

Don't worry about what this means for now.

So each time you type in a password and click Next, the password is cross checked with this pattern.

Who does this cross checking? It is done by a regex engine.

And a regex engine could be a part of any progamming language...

..like Java, Javascript, GoLang or whatever the nerdy engineers at Google use.

Google will get this regex engine to run the pre-defined pattern against the password that we entered, and validate it.

The result of this validation is a pass or a fail.

If failed, Google will prompt you to re-enter the password.

If passed, you will be taken to the next step in the sign-up workflow.

This is a typical use case of regular expressions.

For this use case, it is not necessary that you should be someone working in IT.

Any non-IT person who has signed up for an online account somewhere,

has unknowingly been involved with regular expressions.

Lets see another use case now.

I told you that almost ALL programming languages support regex.

Not only progamming languages, even some text editors come with an inbuilt regex engine.

Notepad does not have one, but Notepad++ has it.

You can install Notepad++ for free, if you don't have it installed already.

In the notepad++ window that you are seeing right now,

I have opened up a csv file for editing.

csv is just a normal text file, the only extra thing is that

it has data divided into fields, with comma as the field separator.

This is a csv file that contains sales invoices.

One record represents one sales invoice.

The first row there is the header row.

We have the field names here which are... ....transaction_date,Product,Price,

Payment_Type,Name,City,State,Country,Account_Created,Last_Login,Latitude,Longitude

The secondfield is the Product name.Now, I am interested in searching all products with the name Product1.

So I'll do a Control F which will bring up the search dialog box

Make sure you set the search mode as 'Normal'

..and enter the search term as Product1.

Instead of Find Next, click on Find all In Current Document.

If you do this, it will open up all search results in a nice little area down beneath.

so 847 hits...so 847 rows have product name as PRoduct1.

It also shows you the line number of the hit. Okay, that was easy.

Next, I am interested in searching all rows where the sales happened...

...in the state of New York United States

So Control F again...

NY,United States.

Find all In Current Document.

We have 41 hits this time with NY,United States. So far so good.

Now I am going to up the ante a bit.

My requirement this time is a bit more complicated.

I want to find all sales records that have product name as either Product1 or Product2

AND the sales should have happened in either the state of New York or New Jersey in United States.

or New Jersey in United States.

So control F. but how do I frame my search term. I cannot come up with a search term that can handle this particular requirement.

Lets unleash the power of regular expressions.

Change the search mode from NOrmal to Regular Expressions.

Only then will the regex engine of Notepad++ come into play.

Now, lets type in our regular expression.

This might look gibberish to you, don't worry. You will be writing much more complex regex's when you are done with this course, trust me.

Find all In Current Document .... and ...voila! We have the data what we requested for.

If you see the results they have either Product1 or Product2 as the product name PLUS they have either NJ or NY as the state.

So we saw how a regex pattern can solve our complicated requirement,

which was otherwise impossible to do with a normal search.

This was our second use case for regular expressions.

You'll come across many such cenarios in your day-to-day work and even non-work life, where you'll need to identify patterns within data sets.

Regular expressions are a versatile tool to have as part of your skill set.

The two use cases we saw in this session are just the tip of the iceberg.

With this, we come to the end of this session on regex use cases. Next, we will see a course roadmap.