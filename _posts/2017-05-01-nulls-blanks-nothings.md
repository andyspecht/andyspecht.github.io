---
layout: post
title: Nulls, Blanks, and Nothings
image: ../img/NullsBlanks/nothing.jpg
tags: [SQL, SQL Server, Oracle, Nulls]
---

One mildly interesting difference between Oracle and SQL Server is how they treat 0-length strings. SQL Server treats them as a kind of thing distinct from a NULL value, whereas Oracle views 0-length strings and NULLS as identical. Consider the following queries in SQL Server:


```sql
SELECT ISNULL(NULL,'Yeah It''s NULL') [isItNull?];
```
---

|__isItNull?__|
|Yeah It's NULL|

---

```sql
SELECT ISNULL('','Yeah It''s NULL') AS [isItNull?];
```

|__isItNull?__|
||

There's a difference. If you run corresponding queries in Oracle, you get this:

```sql
SELECT NVL(NULL,'Yeah It''s NULL') "isItNull?" FROM dual;
```
---

|__isItNull?__|
|Yeah It's NULL|

---

```sql
SELECT NVL('','Yeah It''s NULL')  "isItNull?" FROM DUAL;
```

---

|__isItNull?__|
|Yeah It's NULL|

No difference. These systems also diverge when they measure the lengths of strings made up entirely of spaces. These two queries give the same result, 0, when run in SQL Server:

```sql
SELECT LEN('') AS stringLength;

SELECT LEN('      ') AS stringLength;
```

SQL Server simply [does not care about trailing spaces](https://www.brentozar.com/archive/2017/04/surprising-behavior-trailing-spaces/). However, in Oracle the corresponding queries give us:

```sql
SELECT LENGTH('') "stringLength" FROM DUAL; 
```

---

|__stringLength__|
|(null)|

---

```sql
SELECT LENGTH('      ') "stringLength" FROM DUAL; 
```

---

|__stringLength__|
|6|

Oracle does care about trailing spaces.

Anyway, the differences in how these systems treat 0-length strings and NULLs hasn't meant too much for me in practice, except my SQL Server WHERE clauses sometimes check to make sure a string is not null and has a length greater than 0. The other day, though, a user alerted me to an error that arose specifically from how SQL Server treats 0-length strings. An expression like this was throwing the following error:

```sql
SELECT CONVERT(NUMERIC,'') AS Test
```

> Error converting data type varchar to numeric.

If the application had instead left the column NULL instead of inserting a 0-length string, the query would have run fine.

```sql
SELECT CONVERT(NUMERIC, NULL) AS Test
```
---

|__Test__|
|NULL|

Oracle is able to handle a case like this just fine:

```sql
SELECT TO_NUMBER('') "Test" FROM DUAL
```
---

|Test|
|(null)|

For what it's worth, even though SQL Server runs into an error in this particular case, it is Oracle who recognizes that it's on the wrong side of history and [may need to change](https://docs.oracle.com/database/121/SQLRF/sql_elements005.htm#SQLRF30037):

> Note:
> Oracle Database currently treats a character value with a length of zero as null. However, this may not continue to be true in future releases, and Oracle recommends that you do not treat empty strings the same as nulls.
