---
layout: post
title: Some Trivia on Trailing Spaces in Oracle
image: ../img/NullsBlanks/nothing.jpg
tags: [Oracle, SQL]
---

The other day I was looking at a query that was inexplicably taking forever to run. After a while it began clear that the culprit was some TRIM functions on join conditions. We were working in a database with some serious datatype design issues - specifically many text columns were stored as fixed length CHAR columns with trailing white spaces. It often happened that you would need to join on two CHAR columns with different precisions. As we know, Oracle is a bit more sensitive than other database systems when it comes to trailing spaces, so to make sure the joins worked, the columns were wrapped in a TRIM.

In addition to derailing performance, it turns out that this practice was unnecessary. When performing comparisons with the Oracle CHAR datatype, Oracle will automatically pad the shorter string so that the two strings are the same length. To take [Oracle's example](https://docs.oracle.com/cd/A97630_01/appdev.920/a96624/b_char.htm) if you start with these two declarations

```sql
last_name1 CHAR(5) := 'BELLO';
last_name2 CHAR(10) := 'BELLO   ';  -- note trailing blanks
```

then it is true that last_name1 = last_name2, even though they have a different length. That's kind of wild! If two strings have a different length, can they really be equal? They can be in PL/SQL.

Note that this doesn't work with VARCHAR2. Oracle will not implicitly blank-pad in any comparisons involving a VARCHAR2, including comparisons involving both a CHAR and a VARCHAR2.


