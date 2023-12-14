---
id: 17ykp2q600rsn8r919olte0
title: 23_UnitTesting
desc: ''
updated: 1700315583861
created: 1700315258035
---
Welcome Back.

This lecture will teach you to create unit test cases for your Spark application.

So let's start.

Start your PyCharm IDE and create a new Spark project.

I will also choose the option to create a main.py welcome script.

Let's create it.

So here we are with a brand new project.

I will create a small Spark project and then implement unit testing.

Why am I using PyCharm to create a Spark project?

Why not use a notebook environment?

Can we not do unit testing on the notebook environment?

We can do that.

But doing unit testing on Databricks notebook is a little tricky.

I will show you that also in the end.

But writing unit test cases for your application makes more sense in the local environment.

So let's learn how to do it in your local environment, and then we will take the same to the notebook environment.

Make sense?

Great!

So let's create a Spark application, and then we will implement unit testing.

Right-click on your project name,

go to new,

and create a python package.

Let's name it lib.

Done.

This will create a lib folder.

Right-click the lib folder,

again go to new, and create a python file.

Give a name to it

Great!

So I created a Python library.

I will create two functions in the library.

So open your transformations file and create two functions.

The first function will read a data file, create a dataframe and return it.

The second function takes a dataframe as input.

Then it will apply some transformations to the dataframe and return the result.

Make sense?

Great!

But what am I doing?

I am developing my application using a modular structure.

I have this transformations library which defines two business functions.

My main application will use these functions to implement the requirement.

So let me go to the main.py file.

Delete everything and recreate the application main.

Looks nice.

Correct?

Do you understand what I am doing?

I am importing those two functions I created in the lib/transformations.

Then I create a Spark session.

I am taking the data file name from the command line argument.

Once I have the data file name, I call the load_survey_df() function to load the data file and create a dataframe.

Then I call count_by_country to transform the data and get the results.

Finally, I am showing the results.

I could have written all the code in the main method itself.

But I decided to take a modular approach and implement everything as functions.

Then call those functions from the main.

And this is how we develop real applications.

We implement functions and then use them.

This approach also allows us to do unit testing.

Right?

So in this example, I have two functions: load_survey_df and count_by_country.

These two guys are my two units.

So I can test these two guys and implement unit testing.

Right?

For unit testing, I need sample data.

Testing is not done on large volumes of data.

We always perform unit testing on some controlled well defined test data.

So let me add some test data to the project.

Right-click your project,

go to new, and create a directory.

Name it as test_data.

Now I will paste the sample.csv file into this directory.

Here it is.

You can see the file contents.

We have ten lines in the file; the first line is a header row.

Let me close it and come back to the main.

Great!

So we have a Spark application.

I can package it and deploy it on the cluster.

When I want to run it on large volumes of data, I will pass the data file location from the command line.

The application will read the data file and run on large volumes of data.

I am done.

But what about unit testing?

I have two functions in this application.

So as a best practice, I must implement unit test cases for those two functions.

Right?

Let's do it.

Right-click your project,

go to new, and create a python file.

But this time, we will choose the Python unit test.

Give a name to your unit test file

Great!

So we have a unit test starter file.

By default, Python will use the unit test framework.

There are many other unit testing frameworks.

For example, PyTest is another unit testing framework.

You can choose a suitable unit testing framework as per your project mandate.

However, the default unittest framework is good enough, and we will use the same.

We already have some starter codes here.

But let me delete everything and create everything from scratch.

So I imported everything from the unittest package.

I am also importing SparkSession because we need it.

Then I import the two functions that I want to test.

Then I will create a test class.

I assume you already know Python and how to create unit tests in Python.

If not, I recommend you go through our Just Enough Python course.

We covered unit testing in our Just enough Python course.

Anyways.

So we have the test class now.

The next step is to create a Spark session.

And the best place to create the Spark session is the setUpClass() method.

So let me define a Spark session.

Done.

I created a spark session so we can run some spark code.

What is next?

I want to test two functions.

So I will write two test cases.

Let me implement the first one.

Great!

So I created test_datafile_loading() method.

This method will call the load_survey_df() function and create the sample_df.

Then I want to validate if the load_survey_df is working as expected.

How Do I validate?

One approach is to count the number of rows in the sample_df and check if I have 9 records in the result.

Why nine?

Because I know it.

The test data has nine records.

So If the load_survey_df() is working correctly, it must have loaded nine records.

And that's what I am doing here.

Count the records, and assert if we have nine records.

That's all.

My first test case is done.

Let's create another test case for the second function.

Done.

What am I doing?

I will call the load_survey_df() and create the sample_df.

Then I will call the count_by_country() and collect the records in the count_list.

Why?

Because I want to test if the count_by_country() is working as expected.

So I will call the count_by_country() and collect the result.

Then I will convert the count_list into the count dictionary.

Converting a list into a dictionary is a common practice for unit testing.

Why?

Because a dictionary is a key/value pair.

I can easily refer to a key and get the value for validation.

And that's what I am doing in the following three lines.

I know that if the count_by_country() worked as expected,

it should give 4 for the United States, Canada should be 2, and the United Kingdom count is 1.

And that's what I am asserting in the last three lines.

Make sense?

That's all.

Do you want to run it?

Go to the top

and click the run button.

Worked.

You can see the message: Tests passed 2 of 2 tests.

Great!

Now let's see how you can do a similar thing on the notebook environment.

Let me go to my data bricks workspace.

Let me go to my data bricks workspace.

I will create a new notebook

Then I will paste two of my functions into this notebook.

That's all.

We are done.

I created a library in the earlier setup.

Right?

And this notebook represents my library.

Now create a new notebook

This one is my main.

So I will import my functions from the transformations notebook.

How to do it?

Here it is.

The %run is the same as the import

When we want to import our functions from the notebook, we will be using %run.

This command will not run the notebook.

It will simply import all the functions from the given notebook.

Now I can implement the main logic.

That's all, right?

Do you want to run it?

Run it once and check if things are working fine.

Great!

I can see the output, so it is working.

But what about the unit testing?

Let's create a new notebook

and implement test cases in a separate notebook.

The first step is to import the functions.

Done.

That's how we import functions from another notebook.

Right?

I also have some other imports.

Let me do that.

I need the unittest package, and I also need Spark Session.

So I am importing both.

What is next?

We can copy-paste our test class.

Let me do it.

I have made one change here.

I changed the test data file location.

The local environment can read from the project file.

However, the notebook will read data from the DBFS location.

So I copied the sample.csv in a DBFS location and changed the path accordingly.

That's all.

The rest of the code is the same as earlier.

Now we are ready to perform unit testing.

But this is a notebook environment.

If you run this cell, it will define the test class.

It will not run the test cases.

The PyCharm IDE allows us to run the test cases using a run button.

But the notebook will not do the same.

We have to write some extra code for running the test cases.

So let me do it.

I defined another function here.

This function will create a test suite.

The TestSuite is also part of the unit test framework.

The code is simple.

I am creating an empty test suite.

Then I am adding my two test cases to the TestSuite.

These are nothing but the test functions in my test class.

Correct.

That's all.

Now we can run this test suit.

Let me write code to run it.

That's all.

I create a TextTestRunner and use it to run the TestSuite.

Let me run it and show you.

Worked.

And that's all.

So in the lecture, we learned to organize our project into smaller functions.

So we can write unit test cases for our functions.

Then we learned to write and run test cases in the local environment

and implement them in the notebook environment.

That's all.

See you again.

Keep Learning and Keep growing.