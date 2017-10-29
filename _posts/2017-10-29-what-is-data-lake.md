---
layout: post
title: What (If Anything) is a Data Lake?
image: ../img/DataLake/Lake.jpg
tags: [Data, Big Data, Data Warehouse, Data Lake]
---

"Big Data" is one of those tech buzz phrases that is so ubiquitous that it has become passé. But as big data became commonplace, a new term emerged that I've never quite felt like I grasped: "data lake." I know it has something to do with storing data for (potential) analysis, and it also has something to do with big data. The goal of this post is to pin down exactly what this term means.

Obviously the best place to start any investigation is [Wikipedia](https://en.wikipedia.org/wiki/Data_lake). Here's their current synopsis:

>A data lake is a method of storing data within a system or repository, in its natural format, that facilitates the collocation of data in various schemata and structural forms, usually object blobs or files. The idea of data lake is to have a single store of all data in the enterprise ranging from raw data (which implies exact copy of source system data) to transformed data which is used for various tasks including reporting, visualization, analytics and machine learning. The data lake includes structured data from relational databases (rows and columns), semi-structured data (CSV, logs, XML, JSON), unstructured data (emails, documents, PDFs) and even binary data (images, audio, video) thus creating a centralized data store accommodating all forms of data.

It sounds like a data lake is a place to dump anything that could be considered data. The one part that is perplexing about this is how "natural" the data is supposed to be. The first sentence from Wikipedia describes a data lake as containing data in its "natural format." You might think this means something like "raw data," but in the next sentence it says a data lake can contain "transformed" data that is then used for business intelligence purposes. The general picture is that a data lake is more permissive about what kind of data is stored than a data warehouse, which typically contains data that is processed specifically for business intelligence.

Another good place to look is [Amazon](https://aws.amazon.com/blogs/big-data/introducing-the-data-lake-solution-on-aws/) - the company that wants to store all the data. They give a similar definition that clarifies the issue with raw data:

>The concepts behind a data lake seem simple: securely store all your data in a raw format and apply a schema on read. Indeed, the first description of a data lake compared it to a ‘large body of water in a more natural state’, whereas a data mart could be thought of as a ‘store of bottled water – cleansed and packaged and structured for easy consumption’...A data lake is a bet against the future – you don’t know what analysis you might want to do, so why not just keep everything to give the best chance you can satisfy any requirement that comes along?

Ok, so a data lake is a place to put all of the raw data from your various systems that you can dredge in the future to find what you're looking for. This concept seems simple enough. In today's era we produce a ton of data, you never know what data will be valuable in the future, and storage is cheap, so why not just throw everything in one repository (and call it a "data lake") for safe keeping? Of course, you need at least some minimal way to organize all of this data or else your data lake becomes a dark, unnavigable data swamp.

That seems simple enough. Still, at least one person has called for abolishing the term "data lake," because its meaning has become unacceptably vague. [Uli Bethke](https://sonra.io/2017/08/08/are-data-lakes-fake-news/) argues that the term "data lake" is being used to refer to anything that isn't a standard data warehouse. It refers not only to repositories of raw data but to platforms for self-service analytics or to specific technologies like Hadoop. Bethke's recommendation is to stop using the term "data lake," but perhaps an alternative is to return to the original meaning of the term and insist that "data lake" should apply specifically to large reservoirs of raw data from various sources.

At this point, I feel like I have a sense of what the term "data lake" means and what it doesn't mean. The question underlying all of this is do you actually _need_ a data lake? Or is the creation of data lakes an extraneous side effect of big data hype? I'll leave that question for the reader (or maybe a future post).
