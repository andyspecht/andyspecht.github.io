---
layout: post
title: An Oracle Date Formatting Quirk
image: ../img/dateFormatting/calendar.png
tags: [Oracle, SQL]
---

One of the first things you notice if you come to Oracle from another database system like mySQL or SQL Server is that by default, Oracle is case sensitive when it comes to string comparisons. The first query returns a result but the second two do not:

```sql
SELECT 'that''s right' FROM dual where 'TEST' = 'TEST';
SELECT 'that''s right' FROM dual where 'TEST' = 'Test';
SELECT 'that''s right' FROM dual where 'TEST' = 'test';
```

One thing I didn't notice until recently was Oracle's case sensitivity when it comes to date formatting. Suppose you just wanted to grab the month of the current date. You could execute this query:

```sql
SELECT TO_CHAR(sysdate,'MONTH') FROM DUAL;
```

This returns "OCTOBER". However, you could instead run

```sql
SELECT TO_CHAR(sysdate,'Month') FROM DUAL;
SELECT TO_CHAR(sysdate,'month') FROM DUAL;
```

and you would get "October" and "october", respectively. Same thing when using "DAY" to return the day of the week. Unfortunately this query

```sql
SELECT TO_CHAR(sysdate,'mOnTh') FROM DUAL;
```

just returns the month in lowercase. There are more fun date formatting features that you can read all about in [the official Oracle documentation](https://docs.oracle.com/cd/B28359_01/server.111/b28286/sql_elements004.htm#SQLRF00210).


