---
id: b8yau92xqaf3lu190czi0b7
title: 03_CaseStudy
desc: ''
updated: 1706795569472
created: 1699964280782
---

Acme travels the case study. 
Throughout this course, you will learn about multiple APIs, related practices and concepts. 
I will be using API travels as an example to discuss how these concepts and practices are applied to real use cases. 
Acme Travels was established in 1984. 
As their tagline indicates, their focus is on selling tickets for bus, train and air travel. 
90% of Acme business is conducted over the phone. 
Even in this day and age, they have no digital presence. 
Acme did build a website in 2009. 
This website is managed by the internal IT staff at Acme. 
They struggle to keep the information on this website up to date. 
In 2014, Acme released a mobile application. 
The biggest challenge with it was that it did not show the right data. 
App has too many bad reviews and at this point it is more or less dead as there are no active users. 
Acme Management has a big concern at this time. 
Acme has been consistently losing market share to its competitors in the last couple of years. 
The biggest reason for this decline is because competition has adopted digital technologies to make their offerings more attractive to the customers. 
They are offering these cool mobile applications that the customer can use not only to buy tickets for their air travel and bus travel, but they can also plan their vacations on these applications. 
Competitors are also stealing Acme as partners with affiliation deals. 
At this time, Acme is management has realized that to survive in the travel industry today, they must invest in digital technologies. 
As a result, they have hired a seasoned technologist as the chief technology officer at Acme. 
The CTO spent three months understanding the travel industry and looking at the current state of affairs at Acme, and he came up with this vision a fully automated vacation ecosystem driven by Acme APIs. 
Not only that, he convinced the business to shift their focus from just selling airline and bus tickets to becoming a one stop shop for vacation packages. 
Acme CTO has also put together a digital adoption roadmap for Acme. 
APIs are central to the Acme Digital Strategy. 
Now, before going through the target state architecture, let's take a look at the current state of technology. 
At Acme, Acme has a website and a mobile application. 
The backend for these are built in the PHP logic and hosted on Tomcat Server. 
There is a web backend and there is a mobile backend. 
There is no sharing of code between the web and the mobile backends. 
The data is managed in MySQL. 
Browser connects with the web backend, receives the HTML and images et cetera, and it also uses some XML based services built in the web backend. 
The mobile application connects with the mobile backend and gets the data in Json format. 
Acme has built a large network of partner agents who work with Acme on commission basis. 
When a order is placed by the customer, these agents send the order information by way of email, which is picked up by someone at Acme and they basically use some mechanism to post the data received from the agent into the Acme databases. 
Apart from email, the agents also place orders by way of fax and phone. 
Roughly around 2016, Acme decided to expose soap service to their agent. 
The primary intent behind this soap service was to help agents integrate their systems with Acme directly. 
Many agents tried to integrate this service with their systems, but only a handful of those agents could do it. 
The service was complex and was very sensitive to the versions of the soap standard in use in Acme and on the agent side. 
As a result, today, only a handful of agents are using this service. 
Next, I'll walk you through the target state architecture that the CTO has put together. 
Based on his vision. 
The data will be managed in two types of databases. 
MongoDB, which is a NoSQL database, will be used for managing the vacation package related data, whereas MySQL will be used for transactional data such as credit card payments, partner commissions. 
ET cetera. 
A layer of APIs will be created to manage the data in these two databases. 
This layer of APIs will be protected by way of an API management layer. 
Same APIs will be used by both web applications as well as the mobile applications, unlike the current state. 
There will be no separation of backends for the web and mobile applications. 
Acme will develop partner applications which will use the same set of APIs. 
Not only that, Acme will expose many of these APIs to their partners so that they can integrate their applications, their systems with Acme backend. 
Once the agents have integrated their systems via these APIs, they will not have to use the legacy ways of doing business. 
So no more emails, phone or faxes. 
Their systems will work seamlessly with the Acme Backends via these APIs. 
The CTOs vision is that these APIs will mature to a point where some of these APIs may in fact be exposed to the public domain developers. 
That way an ecosystem will be formed where applications outside of Acme and its partners will use these APIs and over a period of time, as the Acme APIs will gain popularity, the ecosystem will expand to multiple types of service providers within the travel industry. 
Now comes your role in this case study. 
You are the most technical person on the team. 
The CTO has full confidence in your learning capabilities and he thinks that you should be the one handling the most critical part of his vision. 
That is the Acme APIs. 
So he called you in his office and he told you that you have six weeks to achieve three tasks. 
The first one is set up the practices for building Acme APIs. 
Second one is understand how API management works and third one is to build some APIs as proof of concept. 
And all of these need to be done by you in six weeks. 
You are new to APIs, but nothing to worry. 
I will help you get up to speed with APIs. 
You will learn everything you need to know in order to carry out all of these tasks that your CTO has assigned to you. 
So at this time, let's roll up our sleeves and learn how to build some cool APIs.