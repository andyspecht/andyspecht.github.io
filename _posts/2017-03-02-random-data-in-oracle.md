---
layout: post
title: Making Random Data in Oracle
image: /img/randomSqlServer/SQLServerRandomData.png
tags: [Oracle, Random Data]
---

A couple months ago, I wrote a post on how to [generate random data in SQL Server](https://andyspecht.github.io/2017-01-08-random-data-in-sql-server/). At the end of the post, I promised to revisit the topic but in the context of Oracle, rather than SQL Server. As it turns out Oracle provides a built-in function that makes this task essentially trivial.


### A Brief Detour to Oracle GUIDs

Let's start though with the classic tactic of using GUIDs to create random data. The Oracle analogue to the SQL Server NEWID() function is the SYS_GUID() function:


```sql
SELECT SYS_GUID() FROM DUAL 
```

| **SYS_GUID()** | 
| 49B3A4B5D180FC50E05398010A0A2764 | 

Nothing too exciting, but there's an interesting bug (feature?) with how Oracle generates GUIDs. Try running this:

```sql
SELECT SYS_GUID() FROM DUAL 
UNION 
SELECT SYS_GUID() FROM DUAL 
UNION
SELECT SYS_GUID() FROM DUAL 
```

| **SYS_GUID()** | 
|49B3A4B5D180FC50E05398010A0A2764|
|49B3A4B5D181FC50E05398010A0A2764|
|49B3A4B5D182FC50E05398010A0A2764|

These are identical except for the sequential 0,1,2 in the 12th place. As documented by Steven Feuerstein, this [can cause problems](http://feuerthoughts.blogspot.com/2006/02/watch-out-for-sequential-oracle-guids.html) for some applications (See also [this Stackoverflow thread](http://stackoverflow.com/questions/3037462/how-to-generate-a-guid-in-oracle) ).


### DBMS_RANDOM

Now for the main event. Oracle makes our task very easy with a system package ([DBMS_RANDOM](https://docs.oracle.com/cd/E18283_01/appdev.112/e16760/d_random.htm)) that can generate various kinds of random data. It has a few functions, the most useful of which for our purposes is DBMS_RANDOM.STRING. With this function, you have the option to select random uppercase, lowercase, mixed, or alphanumeric strings of any length you like. For example, to select a random alphanumeric 20 character string, you could run:

```sql
select dbms_random.string('X',20) from dual
```

| **dbms_random.string('X',20)** |
| Z9MSVMAFHY2T9NM2L19R |

We can even generate random strings selected from all printable characters:

```sql
select dbms_random.string('P',30) from dual
```

|**dbms_random.string('P',30)**|
|bq0)Vx'g"{/~&#124;5v]N2^m4b_zY*5?4'|

One option that Oracle strangely overlooks is creating a random alphanumeric string that can be composed of both uppercase and lowercase letters. Notice that in our first example above, all of the letters are uppercase.

Still, DBMS_RANDOM is a robust enough package that it provides a great starting place for any quest to generate random Oracle data, and it obviates the need to look at a variety of functions like we did in SQL Server. That's pretty convenient, but it makes for a short blog post.

```
¯\_(ツ)_/¯
```
