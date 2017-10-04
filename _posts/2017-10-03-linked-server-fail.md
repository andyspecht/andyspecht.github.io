---
layout: post
title: Linked Server Fail&#58 TIMESTAMP
image: ../img/Timestamp/timestamp.png
tags: [SQL, SQL Server, Oracle, TIMESTAMP, Linked Servers]
---

The other day I was querying an Oracle database from SQL Server via the Linked Server feature. One of my queries kept throwing errors, and I tried to troubleshoot/rewrite it with no luck. Eventually I got an error message like this:

>The OLE DB provider "OraOLEDB.Oracle" for linked server "MyLinkServer" supplied invalid metadata for column "ActivityDate". The data type is not supported.

That column, "ActivityDate", had the data type TIMESTAMP. TIMESTAMP didn't seem like an especially exotic data type, and you would think Microsoft would be able to handle it. In any case, Microsoft was aware of a similar problem with numeric data types in Oracle, and they posted a link to a fix, but [that link](https://support.microsoft.com/en-us/help/243027) is currently broken.

### How to Work Around the Error

The error occurs when you execute a remote query that involves an Oracle TIMESTAMP column, using the four-part distributed query syntax (see [this post](https://andyspecht.github.io/2017-08-13-linked-server-lessons/) for the basics of linked server syntax). So, something like this:

```sql
SELECT * FROM [Linked Server Name]..[User].[TableWithTimeStamp]
```

The (sometimes) easy workaround is to rewrite your query with the OPENQUERY syntax. This will work:

```sql
SElECT * FROM OPENQUERY([Linked Server Name], 'SELECT * FROM TableWithTimeStamp')
```

What's the explanation for this workaround? When you use OPENQUERY, your SELECT query is executed on the linked Oracle server. When you execute a distributed query, there is some sort of mapping between SQL Server and Oracle facilitated by the ODBC driver that results in the query being processed locally. This mapping doesn't work for TIMESTAMPs. But why not?

### TIMESTAMPs

I did a bit of research to learn more about the date data types in Oracle and SQL Server. The first place I looked is the very thorough _SQL The Complete_Reference_. I learned that TIMESTAMP is an ANSI/ISO SQL standard data type (p. 74), so it's not some weird Oracle-proprietary thing. There was one passage that caught my attention:

>Most SQL implementations now have support for these standard types. One notable exception, however, is that _SQL Server has long used the TIMESTAMP data type for an entirely different purpose, so supporting the ANSI/ISO specification for it presents a very real challenge_" (p. 76, my emphasis).

That might help explain why SQL Server has trouble understanding Oracle's use of TIMESTAMP. So how does SQL Server understand TIMESTAMP? In short, SQL Server uses "TIMESTAMP" as a synonym for "ROWVERSION". ROWVERSION is used for marking different versions of a row. It is an 8 byte value, and each time the row is updated, the ROWVERSION column on the table is automatically updated.

We can see for ourselves. Let's create a table with a TIMESTAMP column and insert some data into it.

```sql
CREATE TABLE Test
(
junk INT,
timestampcol TIMESTAMP,
);


INSERT INTO test(junk) VALUES(1);
INSERT INTO test(junk) VALUES(2);
INSERT INTO test(junk) VALUES(3);
```
When we select from the table we get these results:

|junk|timestampcol|
|1|0x00000000000007E0|
|2|0x00000000000007E1|
|3|0x00000000000007E2|

We can update all of our values, and see how the timestamp column changes

```sql
UPDATE test
set junk = junk+ 1;

SELECT * FROM junk;
```

|junk|timestampcol|
|1|0x00000000000007E3|
|2|0x00000000000007E4|
|3|0x00000000000007E5|


How is this useful? Check out [this post](http://geekswithblogs.net/TimothyK/archive/2014/01/14/introduction-to-rowversion.aspx) for some ideas.

### Conclusion

This conclusion will be somewhat tentative. I would wager that we run into the problem with SQL Server reading TIMESTAMP columns in an Oracle database, because of the weird way that SQL Server uses the TIMESTAMP data type. I don't have proof of this and would love more info on this topic if any readers know more.




