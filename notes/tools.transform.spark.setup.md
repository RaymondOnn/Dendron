---
id: 0h45lrrkauy6sg4jnzvtu91
title: setup
desc: ''
updated: 1698634851500
created: 1698401145825
---

## Setup
### Requirements
- JAVA JDK 8 or JDK 11
- Python 3.6 or higher
- Hadoop WinUtils
- Spark Binaries
- Environment Variables
- Python ID

### JDK 8/11
- Spark runs in a Java Virtual machine so you need JDK on your local machine for installing and running your Spark application.

- The current most recent Java is at JDK 19 but Spark supports JDK 8 or JDK 11. So make sure you have JDK 8 or JDK 11 as Spark is not well tested on other versions.
- Visit jdk.java.net. >> downloads page >> Java SE 11 from the list of all available versions.

- Download the JDK 11 for your platform and extract it.
- Copy the JDK-11 folder and paste it at some permanent place for e.g c:\program files\Java folder.

- We have to set up two environment variables to make it work.
- Use the setx command to set the JAVA_HOME environment variable.
- The JAVA_HOME variable must point to your JDK-11 directory.
- If you want to check, you can restart the command prompt and check it using the echo command.
The second requirement is to add the JAVA_HOME\bin to your PATH environment variable.

You can again use the setx command.

Ensure you include the current value of your PATH environment variable and add JAVA_MOME\bin to the same.

If you made a mistake in this command and forgot to add the current value of your PATH variable,
Execute the Java -version command, and you should see the current Java version.

Spark on windows machine also needs winutils.exe and some DLLs.
Spark was initially developed to run on Linux-based Hadoop systems.
It didn't work on Windows machines but the open-source community created winutils to fix those issues and allow us to run Spark on a windows machine.

So we need two things.
Setup your HADOOP_HOME environment variable
Include HADOOP_HOME\bin to your PATH environment variable.
Download the repository and uncompress it.

You will see the winutils-master directory.

Go inside,

and you will see many other folders.

We don't need all of this.

So I recommend copying the latest version folder and pasting it at another permanent location.

Go inside,

and you will see a bin directory.

That's all.

But we need to set up the environment variables.

Right?

So start your command prompt and do it.

You can use the setx command to set the environment variable.

Make sure you see the success message.

Now add it to the PATH environment variable.

See the success message.

Right?

That's all, we are done setting up the Hadoop Winutils.

Let's move on to the following requirement.

We need Spark binaries.

So let me start the browser and search for Apache Spark.

You should see a link for spark.apache.org.

Follow the link to go to the Apache Spark home page.

Click the download link.

Choose your Spark version.

We recommend taking the most recent Spark version.

When recording this course, the most recent version is Spark 3.2.1.

However, we recommend taking the most recent version even you see a higher version.

Everything covered in this course should work with newer versions also.

You should also choose the Hadoop version.

Choose the most recent version in this drop-down also.

Now you can click the download link and start the download.

Once downloaded, go to the file and uncompress it.

The Spark binary is not a zip file.

It is a tgz file.

So you might need 7zip for uncompressing this tgz on the windows platform.

Let me uncompress it

You should see an uncompressed directory.

Go inside, and you will see a tar file.

This one is also a compressed file.

You can untar this file using the 7zip tool.

Let me do it.

I got another uncompressed directory.

Let me go inside it.

One more directory layer.

Go inside until you see the bin directory.

Here it is.

I will copy the parent directory of this content.

So let me go one level up.

The directory name is a little long.

You can rename it.

Now copy this folder and paste it to a permanent location.

Go inside.

Great!

This is your Spark Home.

But we must set the environment variables.

So start your command prompt and use the setx command to set the SAPRK_HOME environment variable.

Make sure you see the success message.

We should also add the SPARK_HOME\bin to our PATH environment variable.

Let me do that.

Oops!

I see a warning.

Data being saved is truncated.

You may or may not see this warning.

But if you see it, your PATH environment variable is not set correctly.

This warning comes on some windows machines where the PATH environment variable is too long.

And you cannot use the setx command to add something more to the PATH.

However, you can add a more extended PATH environment variable using the Windows UI.

Let me show you the steps.

Right-click This PC and choose Properties.

Go to advanced system settings and then follow the Environment VAriable button.

You will see the Path environment variable.

Edit it.

You can see a long list of things included in the path ENVIRONMENT Variable.

Click the new button.

Paste the Spark HOme directory location.

Also, include the \bin at the end.

That's all.

Click Ok and close everything.

Ideally, you should be done here setting up Spark on your local machine.

Few more patches are required, but Spark should start working on the local machine for most of the students.

If not, I will explain some more steps to fix it.

However, let me test it on my system and see if My Spark on local started working.

Start the command prompt and try running the pyspark shell.

Let me do it.

You should see some messages and finally a command prompt.

IF you see a command prompt and no error messages, you are good to go.

You have Apache Spark running on your local machine.

You might see a warning stating Exception when trying to compute the page side.

But nothing to worry about.

Just press enter key, and you will get the command prompt again.

You can ignore the warning.

Great!

So we are almost done setting up Spark on your local machine.

However, If you see any error running your PySpark shell,

you may want to set up the following environment variables.

In fact, I recommend setting up these environment variables

even if you do not see any error running your pyspark shell.

Setting up these environment variables can save you from seeing unnecessary

while working with Spark on your local machine.

Start your command prompt and set the PYTHONPATH environment variable.

But what is the value?

Go back to your Spark home directory.

You will see a Python directory there.

Go inside.

Copy that path and paste it into the PYTHONPATH variable value.

But that's not enough.

We also need to include one more thing.

Let me show you.

Go inside the lib directory.

You will see a py4j zip file.

Copy the path to the zip file.

Make sure you are taking the full name, including the .zip extension.

Take the absolute path to the py4j zip file and paste it to PYTHONPATH.

Done.

Let me run it.

Worked.

Right?

We also need to set up the PYSPARK_PYTHON variable.

But before that, I need to see my Python installation location.

You can use the where-command to find your Python installation location.

Let me do that.

So I have two Python installations.

I installed Python 3.10, but I already had a default Python on my machine.

I will configure the Python 3.10 location for my Spark.

Let me do that.

Done.

And I see the success message.

Great!

Let me retest the pyspark shell once again.

It worked.

I can see the python command prompt.

Right?

You are done setting up Spark on your local machine.

You can start writing Spark code on the command prompt.

But that's pathetic.

I mean, We do not want to develop large Spark applications working on the command prompt.
