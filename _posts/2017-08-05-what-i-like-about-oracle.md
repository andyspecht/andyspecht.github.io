---
layout: post
title: What I Like About Oracle
image: ../img/Oracle/hq.jpg
tags: [Oracle, SQL Server]
---

For roughly the first two years of my database career, I used SQL Server almost exclusively. When I began to use Oracle, I generally noticed the weird stuff first. Why do you have to select from dual? Why can't you just select from nothing? Why did it take Oracle until 2013 to have identity columns?

But as I've used Oracle more and more, there are certain things that I've come to really like. Here are three of them.

### Packages

Sure you can organize your stored procedures by schemas in SQL Server, but there is nothing like the organization of functions and procedures into the [packages](https://docs.oracle.com/database/121/LNPLS/packages.htm) of Oracle. There are many benefits of packages, but I just like the feeling packages give me when I see my code neatly organized into these self-contained objects.

### Saving Imports/Exports as SQL Statements

This one doesn't have to do with Oracle database but with a standard tool for it: SQL Developer. When you import or export data, you have the option of saving your import/export as a series of SQL statements. This comes in handy, say, when you are importing a csv, and you will want to import it into multiple test databases. Just run the import wizard, get the SQL results, and copy/paste the SQL into the query windows. SQL Server allows you to save your import as an SSIS package, but that is not nearly as fun.

### Nice Defaults

In general, SQL Developer makes the right choices for default settings. Two examples. 1) When you run a select query, SQL Developer will grab your results in batches of 50. This is great when you just need to take a quick look at a table with tons of rows. In SQL Server, you can also specify to only select TOP 50, but Oracle saves you the typing. 2) SQL Developer does not autocommit your DML statements. This means you can more easily undo any mistakes. Yes, you can [change the settings in SSMS to do this](https://stackoverflow.com/questions/1090240/how-do-you-set-autocommit-in-an-sql-server-session), and [good SQL Server practices](https://andyspecht.github.io/2017-01-15-defensive-dml/) can compensate for it, but out of the box Oracle does a better job of protecting your database from you.