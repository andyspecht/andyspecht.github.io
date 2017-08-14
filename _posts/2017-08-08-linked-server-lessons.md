---
layout: post
title: From SQL Server to Oracle#58 Linked Server Lessons
image: ../img/linked/links.jpg
tags: [Oracle, SQL Server, Linked Servers]
---

Recently I was working on a SQL Server database, and I needed to create a linked server to an Oracle database. The goal was to implement a process of moving data from the SQL Server to Oracle and then do some minor data manipulation on the Oracle database. One constraint was that I needed to push the data from SQL Server to Oracle rather than pulling from Oracle. So I installed the Oracle ODBC driver and got to work. Here are a couple lessons I learned.

### Test Distributed Queries vs OpenQuery

In most tutorials on setting up a linked server, the main type of query demonstrated is a three or four part distributed query. Since we are connecting to an Oracle database, our distributed query would look like this:

```sql
SELECT COUNT(*) FROM [linked server name]..[user].[table];
```

And you would use similar syntax for DML queries. Depending on your queries and tables, these queries can take longer than you might expect. When you run these cross-server queries you want to get them over with as soon as possible, since you're coordinating transactions on two different servers. If you are getting bad performance with your cross-server queries, try rewriting them. Specifically, instead of using the [linked server name]..[user].[table] syntax, try OPENQUERY syntax. It looks like this:

```sql
SELECT * FROM OPENQUERY([linked server name], 'SELECT COUNT(*) FROM [user].[table]');
```

It's not the most intuitive syntax, so [check out the official documentation here](https://docs.microsoft.com/en-us/sql/t-sql/functions/openquery-transact-sql). I've found that my queries to linked server often run faster when using OPENQUERY, so definitely try it out.

###Package Up Your SQL

As part of the process I was setting up, I needed to run transactions on the linked Oracle server that _only_ involved data on the linked server. This is not ideal practice, and I found that running a simple delete statement on the linked server took way too long, even with OPEQUERY. However, what sped things up dramatically was to create a procedure with the delete statement on the Oracle database and then call that procedure from the SQL Server. So on the SQL Server side you would run something like this:

```sql
EXEC ('BEGIN
        [Package].[Procedure];
		COMMIT;
	    END;') AT [linked server name];
```

Anyway, that's what worked for me. There are many extra variables to deal with with linked servers, so experiment and see what works for you.