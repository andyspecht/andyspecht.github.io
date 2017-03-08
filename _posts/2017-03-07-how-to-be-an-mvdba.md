---
layout: post
title: How to be an M.V.DBA
image: /img/mvdba/thumbsup.png
tags: [SQL, SQL Server, Database Administration]
---

This post is inspired by my experience with finding SQL Server databases that were started up and never given a second thought. Since the applications these databases support have been running well enough, there's been no need for a DBA, right? This is a testament to the ease of use and reliability of SQL Server, but it's also kind of like buying a new car and then driving it everyday without ever giving a thought to maintaining the car; it'll work for awhile (maybe a long time!), but it'll come back to bite you eventually.

Anyway, this got me thinking: what's the bare minimum of DBAing required for a production, application-supporting database? Obviously if everyone is happy with the application's performance, you don't need to jump in there and start tuning queries and adding indexes. But what is the minimum viable DBAing that every SQL Server should have?


### Pretty Good Practices

So you're working on a project, or you maybe get a request from a user/coworker, and you discover a SQL Server database that you suspect is not being maintained. You are the only DBA or maybe you don't have the title "DBA," but you've become the de facto DBA. What do you do? First, make sure that this database really is abandoned. Ask around and see if anyone knows about it or is responsible for it. Make certain that you are not stepping on anyone else's toes, and also make sure that you are the person to be working on this database.

Assuming you are the person to be working on this database and you have the ability to connect to the database, what should you look for? Here are my suggestions:

#### 1) Make Sure the Database is being Backed Up

This should be obvious. If regular backups are not running, this database is at a tremendous risk for data loss. There's plenty written on SQL Server backups and their best practices, and your backup regimen depends on constraints in your environment and what kind of data loss your business/institution can tolerate. To see if backups have been made in the last couple days, run:

```sql
SELECT database_name, server_name, recovery_model, compressed_backup_size,
backup_size, backup_start_date, backup_finish_date, name, description 
FROM msdb.dbo.backupset
WHERE backup_start_date >= DATEADD(dd, -2, GETDATE())
```

If no backups have been running, that's a severe (and a server) problem, and you need to work with your employer to ensure that this is fixed.

There are additional steps that could (and should) be taken. You should make sure that the backups aren't being stored on the same server as the database. You should [verify the backups](https://www.simple-talk.com/sql/backup-and-recovery/backup-verification-tips-for-database-backup-testing/). These steps will start moving you from the "minimally viable" to the "best practices" territory.

#### 2) Check for CheckDB

Probably (hopefully?) whoever set up the SQL Server knew enough to have backups running. However, it is much less likely that the server is actively monitored for corruption. This is a problem, since corruption can result in big time data loss, and it does not strike predictably. Fortunately, if asked, SQL Server is more than happy to check its databases for corruption. Just run 

```sql
DBCC CHECKDB WITH NO_INFOMSGS
```

There are a few things to make sure you do before running this. First, make sure the database you're running it against isn't huge. If it is, CheckDB will take hours to run. Secondly, and especially if the database is huge, try to run CheckDB at a time when the server is less busy. 

But sitting on a server and manually running CheckDB is not a good strategy long term. You want to automate running CheckDB, and you also don't want to have to babysit this server. You want to be notified if there are any problems. So, one thing you will want to do is to check if database mail has been set up (it probably hasn't). There's lots of good guides on the internet on how to do (I especially like [this one](http://jackworthen.com/2016/06/08/a-basic-guide-to-configuring-database-mail-in-sql-server/). Once you've set up database mail, create a job that runs CheckDB on all the databases and emails you if it finds corruption. Set this job to run once a day. There are many ways to set up the job (check out [this article](https://www.brentozar.com/blitz/dbcc-checkdb-not-run-recently/) for pointers), but my favorite is to just write out the T-SQL. It's simple enough. Better yet, have database mail email you everytime your CheckDB job runs, so you can sleep even better at night. 

#### 3) Alerts

Now that you will be notified if CheckDB finds corruption, you should go one step further and get a notification if the database goes south in other ways. Assuming that you've already set up database mail and have created an agent for yourself, this is easy peasy. Brent Ozar already [wrote out the script to do this](https://www.brentozar.com/blitz/configure-sql-server-alerts/). Just change the operator_name to the one you set up, and chance the notification_method to 1, if you only want email alerts. Run that script, and you will now get an email if there is an error with severity 16 or greater or if errors 823-5 occur. Most of these errors would be caused by corruption, and there's a good rundown of examples of these errors [here](https://sqlperformance.com/2015/04/sql-performance/dealing-with-high-severity-errors-in-sql-server).

#### 4) Security

Security is extremely important, but there are likely constraints that should make you hesitant to change too much. For example, it's usually a best practice to disable the built-in SA account. Unfortunately, some (bad) applications rely on the SA account, and you will break the application if you tinker with the SA account. In addition, you may find that there is sensitive unencrypted data in the database. That's a no no, but the application vendor might insist upon it. I recommend familiarizing yourself with SQL Server security best practices ([this is a good start](https://www.simple-talk.com/sql/database-administration/how-to-get-sql-server-security-horribly-wrong/), but recognize that you may run into limitations when you are taking care of a found SQL Server.

### Conclusion

What's the upshot? If you happen to stumble upon a database and you adopt it, here's the bare minimum of what you should be doing: make sure it is being backed up, make sure CheckDB is running and you will be alerted if corruption is found, and make sure you will be alerted if a severe error occurs. No one will give you an award for DBA of the year, but these steps will ensure that the database is in pretty ok shape.




