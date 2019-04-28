---
layout: post
title: A Dip into SQL History 
image: ../img/SQLHistory/DifferenceEngine.jpg
tags: [SQL,History]
---

The other day I was working with an application and noticed that it committed the sin of storing all its character data with the CHAR datatype in the database. This led to a brief discussion with my coworkers about the VARCHAR vs VARCHAR2 datatypes in Oracle, and I wondered when these datatypes were first introduced. It turns out that finding an answer to this question involves more research than I would have thought, and I still haven't found it. However, looking into this question led down a rabbit hole of material from and about the early days of SQL. Here are two interesting resources I've found so far.

### [Early History of SQL (Chamberlin)](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=6359709)

This is a retrospective article by one of the two people who developed the SQL language (Ray Boyce and Donald Chamberlin). Many of us are familiar with Edgar F. Codd as the "father" of SQL, but Boyce and Chamberlin are the missing link between Codd's seminal paper in the early 70's and commercial implementations of SQL later in that decade.

It is important to recognize that Codd did not invent anything resembling SQL; what Codd pioneered was the idea of a relational database. The languages that Codd was kicking around looked more like what you would find in a mathematical logic class. These languages were the Relational Algebra and Relational Calculus, the latter of which focused on existential and universal quantifiers. Boyce and Chamberlin were inspired by this work and wanted to create an equally robust but more user-friendly language. They can up with the Sequel language (later shortened to "SQL" because of a trademark conflict). Boyce tragically died at the age of 26, and SQL was then further developed by IBM and other commercial vendors.

Chamberlin has an interesting reflection on the intended audience for SQL. They originally intended SQL to be an intuitive language that could be used by non-experts for ad-hoc querying. Chamberlin was surprised by the growth of SQL as a programming language used to implement complex transactions. Still, it is clear from glimpses of the alternatives like Codd's Relational Algebra/Calculus that SQL was the more user-friendly option.

### [The Theory Relational Databases(Maier)](http://web.cecs.pdx.edu/~maier/TheoryBook/TRD.html)

The other resource I've found interesting is a textbook from 1983. It focuses on relational databases at a very mathematical and theoretical level, but for my purposes the last chapter on Relational Query Languages is fascinating because it was written at a time before SQL became the dominate database language. For example, it contains a discussion of a language, ISBL, that was used in one of the first relational databases (the Peterlee Relational Test Vehicle). It also discusses QUEL, the language used with the INGRES database, which is mainly remembered for leading to the PostgreSQL database system. QUEL looks something like SQL in that it uses the WHERE keyword, has a keyword like SELECT called "RETRIEVE", and has SQL-like syntax for aggregation. There is another more obscure language like SQL and QUEL, called "PIQUE" that also uses the RETRIEVE/WHERE syntax. Overall, this chapter is an interesting snapshot of a golden age of relational database development, where there was a convergence towards SQL-like syntax but there were still many viable querying languages.


 

