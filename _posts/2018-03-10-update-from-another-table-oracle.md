---
layout: post
title: Updating from another Table in Oracle
image: ../img/mvdba/thumbsup.png
tags: [Oracle, SQL]
---

One way to actually remember how to do something (so you don't have to always google it) is to explain how to do it. I'm going to do a quick post on the most straightforward way to update a column from another table in Oracle. This is an intuitive task in SQL Server. You would just write something like

```sql
UPDATE tableA
SET tableA.someColumn = tableB.otherColumn
FROM tableA 
     INNER JOIN tableB 
            ON tableA.joiningColumn = tableB.joiningColumn;
```

[The syntax is similar in mySql](http://www.mysqltutorial.org/mysql-update-join/).

However, Oracle does not let you combine a JOIN and an UPDATE like that. To see what you can do instead, we will start by writing a SELECT statement that combines the relevant tables we want to join. For example:

```sql
SELECT tableA.joiningColumn, tableA.someColumn, tableB.otherColumn
FROM tableA 
     INNER JOIN tableB 
            ON tableA.joiningColumn = tableB.joiningColumn;
```

This SELECT statement helps us see all of the relevant columns at one time and will give us some building blocks for our UPDATE statement. To build our UPDATE statement, we are going to need to set our value in tableA equal to a subquery in tableB. Specifically, we are going to look for the relevant value in tableB where the ON clause in our SELECT statement is true. In other words, this is our start:

```sql
UPDATE tableA 
SET tableA.someColumn = (SELECT tableB.otherColumn
                         FROM tableB
                         WHERE tableA.joiningColumn = tableB.joiningColumn);
```

Basically, instead of having an explicit INNER JOIN like in SQL Server, the join occurs in our subquery. But we need to make one addition to ensure that we don't ruin our data (and our day). If we were to execute the previous query, any row in tableA that didn't successfully join to a row in tableB would be set to NULL. That's because in such cases, our subquery returns NULL, so tableA.someColumn is set to NULL. To prevent this from happening, we need to add a WHERE clause to only update columns that match. This is easy enough; we just put our subquery in an EXISTS clause like this:

```sql
UPDATE tableA 
SET tableA.someColumn = (SELECT tableB.otherColumn
                         FROM tableB
                         WHERE tableA.joiningColumn = tableB.joiningColumn)
WHERE EXISTS
			(SELECT tableB.otherColumn
                         FROM tableB
                         WHERE tableA.joiningColumn = tableB.joiningColumn)
```

That's it! The syntax is a little intimidating the first few times you see it, but once you think through it, you'll get the hang of it.




