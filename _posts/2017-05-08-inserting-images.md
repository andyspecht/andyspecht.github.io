---
layout: post
title: Inserting Images and Files Into SQL Server
image: ../img/NullsBlanks/nothing.jpg
tags: [SQL, SQL Server, C#, BLOBS, Images]
---

I'm going to write a few posts examining practices for storing files in SQL Server. This issue first appeared on my radar, when I encountered a 700GB database; 90% of that space was taken up by images and PDF's. The size of the database made running CheckDB and backups a bit of a pain, and I wondered if there were any alternatives to storing these images and PDF's directly in the database. 

It turns out there are alternatives, but before we get to that, let's start with a couple basic technical questions. How do you store a file in SQL Server, and how do you retrieve a file from SQL Server?

### Storing files in SQL Server: The Data Types

We will start by getting one data type out of the way: there is a SQL Server datatype called "Image" that can be used to store any sort of file up to 2GB. It's still available in new editions of SQL Server, but its use is discouraged. In fact, on the [documentation page](https://docs.microsoft.com/en-us/sql/t-sql/data-types/ntext-text-and-image-transact-sql) for Image, Microsoft has the following warning:

> IMPORTANT! ntext, text, and image data types will be removed in a future version of SQL Server. Avoid using these data types in new development work, and plan to modify applications that currently use them. Use [nvarchar(max)](https://docs.microsoft.com/en-us/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql), [varchar(max)](https://docs.microsoft.com/en-us/sql/t-sql/data-types/char-and-varchar-transact-sql), and [varbinary(max)](https://docs.microsoft.com/en-us/sql/t-sql/data-types/binary-and-varbinary-transact-sql) instead. 


So let's use the Varbinary(max) data type. If we knew that we were storing files less than 8,000 bytes, we could instead use Binary or Varbinary(n) [where n < 8,000], but we don't need to worry about that today. As its name suggests, Varbinary(max), is of variable size (like varchar), and it holds binary data. The maximum size it can go up to is 2 GB, just like the deprecated Image data type.

### How to Shove a File into a Database

The syntax for inserting a file into a SQL Server database doesn't exactly mirror the syntax for inserting numbers or text. Let's first set up a test database and a test table.

```sql
CREATE DATABASE chockerBoxoFiles;

USE chockerBoxoFiles;

CREATE TABLE theFiles
(
id INT IDENTITY(1,1) PRIMARY KEY,
fileName VARCHAR(200),
actualFile VARBINARY(MAX)
);
```
It's handy to have the file name stored along with the inserted file to refer back to, since the file is going look like a long string of junk. Here's the syntax for inserting:

```sql
INSERT INTO
    theFiles(fileName,
      actualFile)
SELECT 'C:\Users\Andrew\Desktop\avatar-icon.png', 
   BulkColumn FROM OPENROWSET( 
      BULK 'C:\Users\Andrew\Desktop\avatar-icon.png', 
      SINGLE_BLOB) AS myfile;
```

The unusual feature is selecting from OPENROWSET. In the simplest terms, OPENROWSET is used to access data that lives outside of your SQL Server instance (like on your computer), using OLEDB. [There's some good examples of the abilities of OPENROWSET on this page](https://sql-programmers.com/tsql-openrowset-in-sql-server). Assuming that your SQL Server has access to the file location that you specify, you'll get one row inserted successfully. I inserted this avatar picture that came with [Beautiful Jekyll](https://github.com/daattali/beautiful-jekyll) theme I use for this site:

![squid cat thing](../img/avatar-icon.png)

If you select from our table, you'll see something like this:

| id | fileName | actualFile |
| 1 |  C:\Users\Andrew\Desktop\avatar-icon.png | 0x89504E470D0A1A...|

If you copy and paste the content from the actual file into notepad, you can see that it's a ton of data. A picture is worth thousands of characters!

### How do I get it Out?

Getting your file into the database was pretty straightforward. Getting it out is less so.If you were naive and optimistic (like me), you might try copying and pasting the binary data in your actualFile column into notepad and then saving the file as a .png. Alas, it won't work. You'll get a corrupted file error if you try to open it. 

There are some posts on the internet that detail how to open an image from SQL Server using the command line or ancient stored procedures, but the most likely way that you will be accessing the image is through an application, so we may as well use the resources a more robust programming language would give us. Let's use some C#.


http://www.redmondpie.com/how-to-save-and-retrieve-images-in-c-wpf-application-from-sql-server-database/

https://www.brentozar.com/archive/2015/03/no-more-blobs/

https://www.microsoft.com/en-us/research/publication/to-blob-or-not-to-blob-large-object-storage-in-a-database-or-a-filesystem/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D64525

https://docs.microsoft.com/en-us/sql/relational-databases/blob/filetables-sql-server

http://stackoverflow.com/questions/5613898/storing-images-in-sql-server

https://www.codeproject.com/Articles/4416/Beginners-guide-to-accessing-SQL-Server-through-C