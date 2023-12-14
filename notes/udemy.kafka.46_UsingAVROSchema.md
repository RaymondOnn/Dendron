---
id: 2jnw6brwtmmkd52epsswz3x
title: 46_UsingAVROSchema
desc: ''
updated: 1699882918995
created: 1699882613463
---
Welcome back.

In the earlier lectures,

Â Â we learned to work with JSON serialization.

In this lecture,

we are going to look at the AVRO serialization.

So, let's start.

Avro is pretty much a standard and very well accepted by the community for Bigdata projects.

If you prefer to use Avro,

you can quickly generate POJO from an Avro schema with the same kind of simplicity

that we learned for JSON.

So let's take the same set of schemas that we used for learning JSON part of it.

However, there is a small limitation which requires us to remodel our schema.

In my lecture on JSON schema to POJO,

we created four schema definitions.

First two are perfectly fine,

and we can create them in AVRO as well.

However, the other two schemas were to demonstrate inheritance implementation.

The PosInvoice schema extended the Invoice schema for the JSON implementation.

But that's a big problem for AVRO.

As on date,

Avro generated classes do not support inheritance.

So, we cannot model java extends and implements construct for AVRO.

So, what we can do is to combine the Invoice schema into PosInvoice schema.

If we do that,

we will eliminate the Invoice schema

and will be left with only three schemas.

And that's going to be our baseline schemas for the current lecture.

So, let's create a solution.

We want to define these objects using a simple schema definition language

and automatically generate serializable classes.

So, let's create a new Maven project and define schemas.

Let me create a folder under project resources.

This is the place where I will keep all my schema definitions.

I am creating my first schema definition,

and I will use Avro Schema definition syntax.

The syntax is straightforward.

Define a namespace,

and this would result in a java package.

Then we tell the type of the element,

Â and in most of the cases,

it would be a record which will generate a java class definition.

Then we provide the name of the class.

These three things are required for almost every schema definition,

and they specify the target Java Object and java package details.

The next thing is to define the list of fields.

Each field requires many details, and hence we enclose them in a pair of curly braces.

Then we give a name of the field.

The next attribute is obviously the type of the field.

In our case, the type is going to be a String.

You can optionally allow null for the field value.

Enabling null is explicitly required for Avro schema.

That's it.

We define the rest of the fields in the same way.

Done.

My first Avro schema is described.

Let's define the LineItem schema.

This one is also straightforward.

Give a namespace, type, and then class name.

Finally, we add fields.

All the fields are straightforward.

We are defining a field name and then a type.

Done.

Let's create the last one.

We start this one also in the same way.

Most of the fields are standard, and you already learned it.

Great!

Now comes the delivery address.

We give a field name.

Then, we specify the type.

And the type is "DeliveryAddress."

So the type of this field is the class name of the delivery address schema that we specified earlier.

Make sense?

Good.

Let's do the next one

- InvoiceLIneItems.

What is the type of this field?

It is going to be an array of LineItem objects.

So, the type is not simple.

It is a complex type that requires its own curly braces.

Within the brace,

we define the type as an array and the items as LineItem.

That's it.

This is how you define an array for Avro schema.

That's all.

If you are interested,

you can refer AVRO specification for learning more details about how can you define schemas.

However,

the most common requirements can be covered with what you already learned in this video.

Great!

We are done with the schema definitions.

And now we have all the required schemas.

How do we generate class definitions?

You can use Avro-maven-plugin.

All you need is to add some details in your pom file.

I want to keep my JDK version as 1.8.

So, let me add the maven compiler plugin.

Then, you need avro-maven-plugin.

Let me paste it here.

Ok,

so this is what you need.

These are the plugin's maven coordinates.

And here is the execution details.

In the configuration, we tell the source directory for the schema definition files.

Then we tell the output directory where all the generated packages and classes would land.

Since we are cross-referencing these two schemas,

we need to import those files.

That's all for the plugin.

The build will generate an Avro source code,

and you will need Avro packages to compile the generated code.

So, we also need to include the Avro dependency.

That's all.

Now you can jump to maven lifecycle and compile your project.

Wait for a minute and done.

All of your classes are generated.

And the generated classes are serializable using the Confluent Avro serializer.

We are done.

We learned to generate POJOs in this lecture.

However,

Â you still don't know how to use the AVRO produced POJOs in your Kafka Producers

Nothing to worry,

I am going to cover that part in the upcoming lectures.

See you in the next lecture.

Keep Learning and Keep Growing.