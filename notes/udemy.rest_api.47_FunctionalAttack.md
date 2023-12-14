---
id: 33au880t1zoa02kfgfrziu9
title: 47_FunctionalAttack
desc: ''
updated: 1699967427009
created: 1699965679990
---
Rest API security functional attacks.

My intent for this lecture is to make you aware of some of the common types of functional attacks.

The concept of security and specifically functional attacks cannot be covered in a course like this.

My suggestion is that you carry out your own research.

I have provided a couple of links in the resources section.

Go through those links to better understand the kind of threats that you have to deal with when you

are exposing your API on the Internet.

In this lecture you will learn about what a functional attack is.

And also I'll go over some of the common functional attacks.

Open Web Application Security Project, or Owasp, in short, is a non-profit organization that is working

on putting together best practices for web application security.

With a growing interest in rest APIs, they are particularly putting out some good information on how

to secure your rest APIs.

It is highly recommended that you go through the Owasp website to understand all kinds of security threats

and the best practices around how to prevent your APIs from getting hacked.

The website is ww.owasp.org.

Functional attacks in a functional attack.

The hacker or the attacker tries to understand the functional weaknesses of the API and then the launch

attack using those functional weaknesses.

This is a typical application flow.

The front end.

The browser based application or mobile invokes the APIs that in turn manipulates the data.

The attacker can go through these same applications that are used by the end user or the customer to

launch the attack, or they can directly go to the APIs and launch the attack.

The third way is where they force the customer to launch the attack indirectly.

Obviously there are multiple types of functional attacks.

In this lecture, I'll be covering four common types to gain understanding of other functional attack

types, please refer to the Owasp website.

The first one is the SQL injection.

This is typically carried out against data driven applications.

Next one is the fuzzing.

Fuzzing is where the attacker tries to input random data to understand the weaknesses or the vulnerabilities

of the API.

Third type is the cross-site forgery in which the attacker may force the user to execute a rogue script.

Fourth type is the session or token hijacking in which the attacker carries out the theft of access

token or hijacks the session from user's browser.

Let's go through the details of each one of these.

SQL Injection Attack.

In this attack, the hacker understands the flow of the application.

Here the hacker sees that a form is presented on a browser based application which invokes an API,

and the API then manipulates the data in a RDBMS using SQL statement where the where clause is the most

important part.

Then the attacker in this case can insert sequels or.

Chord in the form data instead of valid data that inserted code or SQL is then passed to the API in

the form of form data.

If the API implementation is using the form data to create the SQL statement, then the SQL statement

is now tampered.

And with this approach, the attacker can read the sensitive data.

They can manipulate or delete the data.

In fact, they can even take admin action on the database, such as shutting it down.

This attack can be carried out not just with SQL but with XPath, Jsonpath and XSLT.

Next, I'll demonstrate to you a simple SQL injection attack.

This is a simulation.

It's a simple node application which is available on GitHub if you want to try it on your own.

For this demo I'll be using the SQL fiddle.com.

This is a site where you can test out the MySQL schemas and MySQL queries.

So let me just create the schema here in the SQL injection dot SQL file.

I have my schema ready, so I'm just going to copy it and now build the schema.

Now schema is ready.

Let's just run a query here.

This query is looking for user by the name John and password 456.

So as you can see, I've retrieved one row for John with the password.

Four, five, six.

Now, let's assume that our node application for simulation is going to use this kind of a query to

log in users.

Let me show you the code now.

So in the code in App.js, I'm reading the static files from slash public.

I have an index.html there which pops up the login form and SQL injection is the class where I've implemented

the slash login route.

So in the SQL injection, what is happening is that the root slash login received two query parameters

name and password, and then a SQL is created which says select star from users where name is equal

to the received name and password is equal to pass.

And then just assume that this SQL is executed against the database to log in the user.

Let's see this in action now.

So this is the index dot HTML from our application here.

I'll put in the name John and password is four, five six.

Submit and as you can see user logged in as John and why.

Because select star from users where John name is equal to John and password equal to 456 worked out

fine the last time we tried it on the SQL.

Now let's go back to the page and try out another thing.

Now, this time I will put in admin and the password I'll just put junk.

Who cares what it is?

This is where I'm showing you how SQL injection attack will log in anyone with junk password.

So here I'll have admin and then I'll put a single quote, then a dash, another dash, a space.

And then send.

This information to the login.

Now guess what?

I'm logged in as admin.

Let's copy this query and try it out.

In the fiddle to see what happened.

We'll copy that query here.

And hit run SQL.

Guess what?

The query is valid and one row was fetched, which is admin.

So if your code was using the parameters query parameters the way they were coming to it, then this

is a potential issue and we just carried out a SQL injection attack.

Now, if you're wondering why it worked, it's because this character here, Dash Dash is actually a

commence character.

So what's happening here is that the SQL, in effect, is not checking for password, it is just this

part.

So you're simply saying select star from users where name is equal to admin and log the user and now

the user has control.

Fuzzing attack in the fuzzing attack, the hacker tries to understand the internal implementation or

the loopholes exposed by the API and then leverages those loopholes or issues of weaknesses to launch

the attack.

Fuzzing also refers to a testing technique so the attacker interacts with the API by sending random

inputs.

The response back with some errors or some response data.

The attacker analyzes the data to discover the weaknesses and vulnerabilities.

Once the attacker understand the weaknesses and the vulnerabilities they launch, the denial of service

attack or some other attack to manipulate the data.

For example, test the API with fuzzing in mind.

Also, do not send the internal errors.

For example, if you are invoking SQL queries and you are getting exceptions, do not send those SQL

queries.

Exceptions as is, wrap them with API errors and don't show any internal error cross-site forgery.

In this kind of an attack, the attacker forces an end user to execute a script.

The attacker sends a link to the user and this link may be sent in an email or it may be on a web page.

When the user clicks on the link, they receive a hacker's script and the script gets executed.

When the script gets executed, it invokes the API API response back with some data which the which

the hacker can access.

Owasp has done a great job in putting together some suggestions around how to counter these kind of

attacks.

But I'm putting out here is taken from Owasp.

I suggest that you read the document and understand how this attack is carried out and what are the

countermeasures you can take.

So what Owasp suggests is that you use post instead of get break the transaction into smaller steps

and add custom headers token and session hijacking token and session.

Hijacking is very similar to the cross-site request forgery.

The attacker sends the link to a user.

The user clicks on the link, receives the hacker script.

The hacker script now has access to the session and access tokens.

At this point, the script can invoke the API using the access token.

The other thing the script can do is that it can send the access token to the hacker, who can then

invoke the API using that access token.

Owasp has a couple of suggestions to counter these kind of attacks.

First one is ensure expiry of tokens do not have tokens that have a very long validity period.

Second one is use complex patterns for the creation of tokens.

And the third one is don't just rely on access tokens, use additional security headers for implementing

security for your APIs.

Unfortunately, there is no silver bullet to solve this problem.

So how do you protect your APIs, your user, your data from such attacks?

In my opinion, the best way is testing come up with test cases that will represent the functional attacks

on your API and use the findings from these test cases to make changes to your APIs.

Couple of other things.

Follow the best practices for coding and rest APIs.

Create a process for code reviews.

Keeping in mind the kind of functional attacks that your APIs susceptible to.

Test and monitor continuously.

This is an important one.

Invest in tools.

There are many good tools available out there that let you carry out the simulation of functional attacks.

Use these tools.

Select an appropriate security model for.

This goes without saying.

Consider an API gateway or API management solution for preventing the attack calls from reaching your

API implementation.

I'll cover this topic in details in the section on API management.

Set aside budget for API testing.

Many times I hear that the teams do not have the budget to conduct API testing.

Not a good idea.

In the very beginning of the project planning phase, you must set aside budget and resources for testing.

Let's summarize in this lecture I covered functional attacks.

Functional attacks are carried out by hackers by exploiting the functional weaknesses or vulnerabilities

of your APIs.

Owasp, which stands for Open Web Application Security Project, is a non-profit organization that has

been putting together all kinds of best practices and suggestions around how to protect your APIs from

such attacks.

Highly recommended that you go through the Owasp website.

Although there are many types of functional attacks that the hacker can carry out.

In this lecture I covered SQL injection fuzzing, cross-site forgery and session token hijacking.

The question around how to protect your APIs against such attacks.

There is no silver bullet.

Follow security.

Best practices for implementing your APIs and thorough testing is the way to go.