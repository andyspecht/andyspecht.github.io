---
layout: post
title: Index Basics for SQL Server
image: /img/SQLServerIndexes/book.jpg
tags: [SQL, SQL Server, Indexes]
---

_Note: I wrote most of this post over a year ago, but I wanted to revisit it and put it with the rest of my SQL posts. This post briefly covers the basics of what indexes in SQL are, specifically in the context of SQL Server. It's at a very general level, and we won't go into the thorny details of indexing strategies (at least this time)._

Indexes are used to guide your queries to the relevant data as efficiently as possible. Without indexes your queries would have to scan every row of the table in order, until it found what it was looking for. Thus, well-designed indexes will speed up the execution time of your queries.

### Clustered Indexes

Indexes can be divided into two main types: clustered and non-clustered indexes. Clustered indexes literally change the physical structure on a table to create the relevant ordering of the rows. If we think of our database as a book, a clustered index is the ordering and numbering of the pages in the book. This means that there can only be one clustered index on a table, though the clustered index can consist of multiple columns. A table without a clustered index is called a "heap.”

By default, SQL Server will create a clustered index on a table, when you designate a primary key. This clustered index transfers the ordering provided by the primary key to the physical structure of the table. [It is possible to remove the clustered index created by the primary key and create a clustered index on non-primary key columns](https://blog.sqlauthority.com/2016/08/14/drop-clustered-index-primary-key-column-interview-question-week-084/). Also, if you create a clustered index on a set of columns that contains duplicate values, SQL Server will “uniqueify” the duplicate values. What this means is that it will append 4-byte values to the end of duplicate values to make them unique.

### Non-Clustered Indexes

Unlike clustered indexes, you can have an indefinite number of non-clustered indexes on a table (well 999, in SQL Server 2008 and later). Non-Clustered Indexes are _virtual_ orderings of a table and do not change the physical structure of the table. They exist separately from the table and take up additional storage. What a non-clustered index does is create an ordering on a column or collection of columns, where for each value there is a pointer that refers to a row address for the value. To use our book analogy, a non-clustered index is like a subject index in the back of the book, with a list of topics and a pointer to a specific page number in the book.

One action that creates a non-clustered index is the creation of a unique constraint on a column. In fact, adding a unique index and a unique constraint have exactly the same effect on a table.

### Pros and Cons of Indexes

* Clustered indexes typically result in faster read performance than non-clustered indexes.
* Non-clustered indexes take up storage space; clustered ones do not.
* Indexes can slow down insert statements. To a lesser extent they can slow down delete and update statements. The reason is that indexes themselves must alter in light of these changes to the table

## Some Query Templates

```sql
sp_HelpIndex tblname -- returns indexes on tblname
```

```sql
CREATE [Unique] [Clustered/Nonclustered] INDEX IX_tblname_column
ON tblname (columnName [ASC/DESC])  
--creates an index on your table.
--bracketed words are optional
```

### Where to go from Here

#### Indexing Basics


* [Kudvenkat’s videos #35-8](https://www.youtube.com/playlist?list=PL08903FB7ACA1C2FB)
* [SQL Server Index Basics](https://www.simple-talk.com/sql/learn-sql-server/sql-server-index-basics/)
* [14 SQL Server Indexing Questions You Were Too Shy To Ask](https://www.simple-talk.com/sql/performance/14-sql-server-indexing-questions-you-were-too-shy-to-ask/)
* [Indexes for Kids](https://www.brentozar.com/archive/2017/01/indexes-for-kids/)

#### More Advanced
* There's lots of good stuff by Kendra Little. See [her blog](http://www.littlekendra.com) and [some of her older content on Brent Ozar](https://www.brentozar.com/archive/2011/09/kendra-little-explains-how-design-smarter-indexes/)