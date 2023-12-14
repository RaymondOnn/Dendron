---
id: dbsiwm5bct8ssx0eq2zezep
title: 06_WhatIsAnAPI
desc: ''
updated: 1699966218491
created: 1699964428140
---
Introduction to APIs.

In this lecture, I'll share my experience as a web developer who built an online DVD store in year

2000.

My objective is to provide you some historical background on how the API technology has evolved in the

last two decades.

I'll start with year 1995, when the Internet Movie database website was launched.

The idea of this website was that there was a huge database of movies and anyone could connect to this

website and get the information and images from that movie.

I used to spend hours on the go on this website.

A year or so later, Fedex released the tracking feature on their website.

Anyone could use their website, provide the tracking number for a package, and get the real time information

about the package in transit.

This feature was considered to be revolutionary at that time.

Fast forward to year 2000.

This is the story of my first web project.

An investor hired me to build an online DVD store.

It was a typical e-commerce portal.

He had some links with some DVD distributors, so he wanted to sell those DVDs online.

He gave me two specific requirements that he wanted to build in this website.

The first one was that when the users were browsing the DVDs, they should see the images from the movie.

They should be able to read the ratings.

They should be able to get the storyline, the cost, etcetera.

Essentially what that meant was that we had to pull all the information about the movies from the IMDb

website and then show it to the user as part of the DVD store website.

For the second requirement, he told me that the DVD store will ship out the DVDs by way of Fedex,

and the users should be able to track the package on the DVD store website.

And to do that, I'll need to somehow get the tracking information for the package from the Fedex website.

So I carried out research to figure out how to address these requirements.

And as part of my research, I found this article on data scraping and it also talked about web scraping.

The idea was simple You will write a tool to get the HTML for the desired web pages, and this tool

will then extract the information from the pages by parsing the HTML.

The data scraped by this tool could be saved in the database or presented to the user in real time.

I was excited to learn that many developers at that time were using this technique, so I gained a lot

of confidence.

So let me show you how I built my first feature for fetching the movie information.

I built this web scraper in PHP that would receive the movie information from the IMDb website, carry

out the parsing, and then store the images and all other movie data in a database and a file system.

The website was built in PHP and when the website will receive Http requests from the browser, it will

pick up the information about the movies from the storage, wrap it with an HTML and send it back to

the browser, and the movie information will then show up in the end user's browser.

This gave the end user a seamless experience of buying DVDs while learning more about the movies.

For building the real time tracking feature, I created another web scraper which could read the pages

from the Fedex website.

The way it worked was my PHP based DVD store website will receive the Http request from the browser.

When receiving the request, my website invoked the web scraper, which in turn executed Http.

Get on the Fedex website, received the HTML from the Fedex website, parsed the HTML, extracted the

tracking information, and then sent it back to the code in my website, which used to then wrap the

tracking information in some HTML and send back to the end user's browser.

And this worked very well.

Finally, after three months of hard work and a lot of tweaking to the web scraping code, a DVD store

website was launched on day one.

We sold roughly 20 DVDs, mostly to friends and families.

I was extremely happy not knowing what lay in store for me.

This was the beginning of my nightmare.

Let me give you an idea why I'm calling it a nightmare.

On day ten, movie download web scraper broke down due to a change in the HTML format on the IMDb end.

Day 14 Fedex form name changed, so the tracking information stopped coming from the web scraper data.

21.

The movie download started to return page not found because the URL for the movies was changed.

Day 39.

The images were not correct because the developers at IMDb decided to switch the placement of images.

Day 45 Fedex started rejecting our request and I had to add a new header to the Http request, and this

continued for a long, long time.

It was an extremely painful experience to keep the website working with these web scrapers.

After six months of struggle, I realized that web scraping was not easy to maintain, as it was very

sensitive to any changes in the source website.

Like many other e-commerce websites or online DVD store also died after roughly five years in 2006,

I came across a news article.

Fedex releases http XML, RPC for tracking and what that essentially meant was that the Fedex continued

to support their tracking feature on the website, but they also introduced a new feature on their website

which could be used by any other website.

The idea was simple There was a separate URL which any website could reach out to and get the information

on package in transit.

The data from this new URL was an XML format.

These two endpoints were independent and the Fedex website team could manage their website code and

content without impacting the users of the tracking endpoint exposed by the Fedex servers.

This remote procedure call mechanism exposed by Fedex was the API for tracking the packages.

API stands for application programming interface.

A more formal definition will be a user interface to data and systems that is consumed by applications

rather than humans.

So in this visualization, this is the API endpoint URL, which is expected to be used by a machine

or a website.

One important aspect of the API that should be noted is that there is a well defined contract between

the consumer and the provider.

This contract is defined by way of the XML request and response structure.

Unlike the HTML pages, the XML request and response structure will not change randomly.

JavaScript object notation format or Json was developed in year 2000, but it was standardized in year

2013.

Once it was standardized, many platforms and languages started to provide native support for Json.

Over the last many years, Jason has gained a lot of popularity among the developers.

In fact, it has become the first choice for formats for the API developers.

So what that means is that earlier we used to define contracts with XML as the request and response

structure, but with the popularity of Json, more and more APIs are now supporting Json request and

response structures.

Fast forward to 2020.

All enterprises today have API listed as a critical element of their digital strategy.

These organizations are using these APIs for building their internal applications, for exposing their

assets to the partners and even the public domain developers.

Let's try out an experiment.

Go ahead.

Think of a mid-size to a large organization.

Just put in the name, for example, Citibank and just add developer API at the end of it.

Here is the Citi Developer Portal.

Check out all the APIs available on Citi Portal.

Let's try out another one.

Let's say Expedia Developer API, and here is the link to the Expedia APIs Developer Portal.

I suggest that you try out the search for the organizations of your interest and check out their APIs.

Time to summarize, in this lecture, I provided you an overview of how the API technology has evolved

over the last two decades.

I also covered the definition of an API API provides programmatic access to data and system.

There is a contract between the API provider and the API consumer in the form of well defined XML or

Json structures for the request and responses.

Most modern APIs support the Json format, but many APIs support both Json and XML format.