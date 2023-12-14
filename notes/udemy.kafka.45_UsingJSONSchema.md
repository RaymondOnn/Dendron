---
id: cahi8snqfjl93kkswhqn027
title: 45_UsingJSONSchema
desc: ''
updated: 1699882902109
created: 1699882579907
---
Welcome back.

As promised in the earlier lecture,

we will be creating some schema definitions in this lecture

and generate POJOs out of those schemas.

Let me define a problem statement that we will be solving in this lecture.

We want to be able to model an Invoice event.

Our invoice is a complex Java Object that contains simple fields

such as Invoice numbers, Created Time, Customer ID, Total Amount, etc.

These fields could be of String, Long, Number, and Integer types.

Other than these simple fields,

we also want to have an array of Invoice Line Items.

An invoice Line item is another complex object which is defined separately

and contains item details such as item description, price, and quantity.

We also want to define a separate object for a delivery address containing fields

like address line, state, pin code, etc.

Finally,

we want to combine all of these objects to create POS invoice.

The POS Invoice inherits the Invoice object.

So,

we automatically get all fields and array of line items from the Invoice Object.

However,

we also want to add some additional fields

such as store id, cashier id, delivery type, and delivery address.

That's all.

We will be transmitting POSInvoice to Kafka broker.

However,

I have modeled various objects separately and also included a scenario for extending a class.

The example represents a typical Java Object-Oriented modeling approach.

Right?

And that's what we wanted to learn.

How do we model our events as we model the usual java classes?

Now,

let's look at the solution.

We want to define these objects using a simple schema definition language

and automatically generate serializable classes.

I am going to use an open-source project named jsonschema2pojo.

You might find other better alternatives

or build your own custom tool for the purpose.

However,

JsonSchema2Pojo is a reasonable choice for being able to produce Java as well as Scala code.

Let's create a new Maven project

and define schemas for our objects.

Let me create a folder under project resources.

This is the place where I will keep all my schema definitions.

I am creating my first schema definition,

and I will use the JSON syntax.

The syntax is straightforward.

Define the type as an object.

Then we tell the fully qualified name of the target java type.

These two things are required for almost every schema definition,

and they specify the target Java Object

and java package details.

Every field in the target class should be defined inside the properties.

So,

what fields do we have for Line Items?

ItemCode, ItemDescription, ItemPrice, ItemQty, and total value.

Now, you must define the data type for these fields.

So, the type for the ItemCode is a String.

The description is also a string.

The price should be a number.

We take quantity as an Integer.

This one is a decimal number.

You can check out the documentation for more details about available data types

and the corresponding Java types that the schema is going to generate.

There is a lot more information in the document.

I recommend you go through it once.

Great!

So we define one schema.

Let's create another one.

This time, I want to create the Delivery Address.

Define the type and the Java Type.

Then, add a list of properties.

We did a similar thing for Line Items, right?

This one is also pretty much straightforward. Right?

Let's do the next one.

The Invoice.

Â We create Type and Java Type.

Then we define properties.

Ok,

I wanted to define the create time as a Long value.

The documentation doesn't have anything for creating a Long.

However,

it allows me to set a Java Class Name.

And my Java Class could be any existing Java Class in the application's classpath.

So, I can take a Long.

All other fields are similar to the earlier schema.

Â Ok, the last one is Invoice Line Items.

And this one should be an array.

But an array of what?

We also need to define the items of the array.

Let's define it.

The type of each element of the array is an object. Right?

And for the detailed definition of the object,

you can refer to LineItems.json file.

Correct?

If we want to refer to an existing class?

We refer it using JavaType as we did for the Long.

Instead of Long,

it could have been a custom created class like MyClass or something else.

But if we want to refer a class that is not yet generated,

we set the type as an object and provide a reference.

Great!

We are left with the last one.

The PosInvoice.

As usual,

define the type and the Java Type.

We wanted the POSInvoice to extend the invoice.

Right?

I do not have a requirement to implement a Java Interface in this example.

However, the documentation says, you can do that also.

Ok, let's come back and finish our POSInvoice definition.

Rest of the fields are straightforward.

The delivery address is an object which is not yet generated,

so we add the file reference.

Done.

We have all the required schemas.

How do we generate class definitions?

The jsonschema2pojo comes as a maven plugin.

All you need is to add some details in your pom file.

Let me paste it here.

Ok, so this is what you need.

These are the plugin's maven coordinates.

And here is the execution details.

In the configuration, we tell the source directory for the schema definition files.

Then we tell the output directory where all the generated packages and classes would land.

You will also need the Jackson data bind.

So, let me add the dependency for the same.

I want to keep my JDK version as 1.8.

So, let me add the maven compiler plugin.

You will also need Apache Commons.

We don`t need it, but the JSON schema to POJO requires that.

That's all.

you can jump to maven lifecycle and compile your project.

Wait for a minute and done.

All of your classes are generated,

and the class definition comes with Jackson annotations.

These annotations make these classes serializable using the JSON serializer

and deserializer that I have included in the examples of this course.

We are done.

We learned to generate POJOs in this lecture.

See you in the next lecture.

Keep Learning and Keep Growing.