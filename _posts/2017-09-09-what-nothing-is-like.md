---
layout: post
title: What Nothing is Like&#58 The Behavior of NULL
image: ../img/NullsBlanks/nothing.jpg
tags: [SQL, SQL Server, Oracle, Nulls]
---

In everyone's SQL career there comes a time when you run a query and you get a bizarro result set. After some investigation, you discover that it's because of how you are using NULL in your where clause. NULLs still surprise me. I'm going to briefly touch on a bit of philosophy of what a NULL is and then give one example of where it can behave strangely in SQL.

### What _is_ a NULL?

A NULL value is an absence of any value. It may look like something in a result set, but it's really an indicator that there is nothing in that spot. Is NULL equivalent to an empty string? [It depends who you ask](https://andyspecht.github.io/2017-05-01-nulls-blanks-nothings/). In general, the answer is "no", but Oracle database in no way distinguishes between empty strings and NULLS. Philosophically this is strange, since a NULL doesn't seem like the same type of thing as a string; you can have NULLS in columns of any datatype (numbers, dates, BLOBS, whatever!). As a quick aside: C# does a nice job of explicitly distinguishing between empty strings and NULLs, since it has a [String.IsNullOrEmpty](https://msdn.microsoft.com/en-us/library/system.string.isnullorempty(v=vs.110).aspx) method that does what it sounds like - it checks for two different things.

### Concatenating NULLs

NULL values can ruin your day when you try to concatenate strings. For example, the following query will return NULL in SQL Server:

```sql
SELECT 'part1' + NULL + 'part2'
```

A NULL anywhere in a concatenation "pollutes" the entire concatenation and makes the result NULL. Here it can be helpful to think of NULLs as unknown or indeterminate values. If you add an unknown element to your string, then you can't know what the resulting string will be. However, it is possible to change this behavior in SQL Server. To do so, you would run the self-explanatory:

```sql
SET CONCAT_NULL_YIELDS_NULL OFF
```

To check the setting in your current session, simply run

```sql
SELECT SESSIONPROPERTY('CONCAT_NULL_YIELDS_NULL')
```

However, in Oracle we get completely different behavior. An equivalent query

```sql
SELECT 'part1' || NULL || 'part2' FROM dual
```

returns "part1part2" ! Here's what Oracle has to say about the matter:

>Although Oracle treats zero-length character strings as nulls, concatenating a zero-length character string with another operand always results in the other operand, so null can result only from the concatenation of two null strings. However, this may not continue to be true in future versions of Oracle Database. To concatenate an expression that might be null, use the NVL function to explicitly convert the expression to a zero-length string.

Essentially, concatenating NULLs does not result in string contamination, because Oracle (for now) views NULLs are zero-length strings; it chooses to gracefully glide right over NULLs in string concatenation. This behavior doesn't translate to mathematical operations, though. Both SQL Server and Oracle will return NULL, whenever NULL is added,subtracted, multiplied, divided, etc.

### Further Reading

There is much more to know about how these mysterious non-values. For a good SQL-Server-focused article, check out ["How to Get NULLs Horribly Wrong in SQL Server"](https://www.red-gate.com/simple-talk/sql/t-sql-programming/how-to-get-nulls-horribly-wrong-in-sql-server/) by Robert Sheldon. For an Oracle perspective, see [this Code Project article](https://www.codeproject.com/Articles/704385/Gotcha-Oracle-Null) by Sam Varadarajan.
