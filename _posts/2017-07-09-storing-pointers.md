---
layout: post
title: Storing Pointers to Files in SQL Server
image: ../img/avatar-icon.png
tags: [SQL, SQL Server, Images, Files]
---

Recently, we've looked at how to store images and files in SQL Server - specifically how to [insert them into](https://andyspecht.github.io/2017-06-12-inserting-images/) and [open them from](https://andyspecht.github.io/2017-06-23-extracting-images/) the database. Storing files in the database is often considered a not great practice. The most canonical guidance comes from a [2006 paper by Microsoft](https://www.microsoft.com/en-us/research/publication/to-blob-or-not-to-blob-large-object-storage-in-a-database-or-a-filesystem/) which argues that it's fine to put BLOBs under 256KB directly in the database but that files over 1MB should reside outside of the database. Let's talk about how we can store these files outside of a database, when we are using a SQL Server-driven application.

### Pointing to Files

The gist of what we need to do is very straightforward. Instead of storing an actual file in the database, we will store a reference to the file that our application can read from the database and use to find and open the file from the file system. We can modify our set up that we created for our previous experiments with images in databases.

```sql
CREATE DATABASE chockerBoxoFiles;

USE chockerBoxoFiles;

CREATE TABLE theFiles
(
id INT IDENTITY(1,1) PRIMARY KEY,
fileName VARCHAR(200),
filePath VARCHAR(260)
);
```

We have a filePath column that will store a path in text, instead of a column that stores an actual file in VARBINARY. We will insert a path to that weird cat-octopus image again.

```sql
INSERT INTO
    theFiles(fileName,
      filePath)
SELECT 'Cat-Octopus','C:\Users\Andrew\Desktop\avatar-icon.png';
```

We can use a basic PowerShell script to open our image using the path from our database.

```powershell
$Server = "(LocalDb)\MSSQLLocalDB";         # SQL Server Instance.            
$Database = "chockerBoxoFiles";    

$Sql = "SELECT filePath
	    FROM theFiles
	    WHERE fileName = 'Cat-Octopus'";  

# Open ADO.NET Connection            
$con = New-Object Data.SqlClient.SqlConnection;            
$con.ConnectionString = "Data Source=$Server;" +             
                        "Integrated Security=True;" +            
                        "Initial Catalog=$Database";            
$con.Open();  
                
# New Command and Reader            
$cmd = New-Object Data.SqlClient.SqlCommand $Sql, $con;            
$rd = $cmd.ExecuteReader(); 
$rd.Read();
$image = $rd.GetString(0);  

invoke-item $image
```
When we run this script (assuming the paths are correct) the screen pops up with our image. That wasn't hard at all!

### The Upside and the Downside

The main advantage of this approach is that you get larger objects out of your database. This means that maintenance like backups, restore, and checkdb are going to run faster. That's a pretty big advantage. Still, there is a downside to consider. This approach results in your data being splintered into separate groups. You can't just backup your database and have all your data. You have to backup both your database and the file system that contains the objects referenced by your database. 

That seems simple enough, but it exposes you to the risk of your database and your files becoming out of sync. In the case of a disaster recovery situation, your last good database backup might be from yesterday at 3pm, but your last file system backup might be from yesterday at 6am. If you restore these two backups, the pointers in your database might not match the objects in your file system. 

One really interesting example of this happening is with the picture-hosting company Picturelife. I heard about it from [an episode of *Reply All*](https://gimletmedia.com/episode/71-the-picture-taker/). After being contacted by a user of Picturelife, who found that all of her pictures vanished one day, *Reply All* investigated what happened to the company and tried to find out where all the pictures went. This is what they learned (my emphasis):

>ALEX: But then he got bad news from the people who run the data center.
>
>JONATHAN: Then they ask us to decrease the footprint of the platform by two-third [sic] in 60 days. What does it mean? It means that Picturelife was running on 120 servers–
>
>ALEX: Mhm.
>
>JONATHAN: –and we had to, uh, bring it down to 40.
>
>ALEX: And so, he had this decision to make, basically. Which was, he could either put up a thing that says, “Hey, I’m shutting down Picturelife. You have 60 days to get your stuff, otherwise it will be totally deleted.” Or, he could try to save the company. So … he crunches the numbers and he says, “I think I can do this. I think I can move everything down to just 40 servers.”
>
>...
>
>ALEX: So, Jonathan furiously got to work trying to move all of these pictures. **The problem is, he had to move everything so fast that he couldn’t update the database that said where the photos lived.**
>
>PJ: I don’t understand.
>
>ALEX: So, imagine a library loses its lease and has, like, a couple days to move to a new space. And, so, in order to move in time, they just toss all of the books in the back of a U-Haul without any concern for order, and then they move to the new space.
>
>It’s like, all the books are still there, but they’re totally out of order. So, if you ask the librarian, like, “Hey, can you help me find To Kill a Mockingbird? The librarian doesn’t know where to look.
>
>**The librarian is, basically, the database in this situation. Like, it knows the pictures are all still there, it just doesn’t know where to look for them. So when you go to the website, what you’re seeing are just blank s- are just these color swatches.** So he basically had to break the service to save the service.

In short, Picturelife had to do a rush migration of their images and were unable to keep their database pointers in sync. As a result, users lost access to their pictures. It's a fascinating story, so definitely check out the whole podcast. Anyway, it's a great example of what can go wrong with this approach to storing images.

### Alternatives

Starting in SQL Server 2008, there is a feature called "Filestream" that functions sort of like a hybrid between the BLOBS in the database approach and the pointers to the file system approach. Enabling this feature is somewhat involved, and other people have done a very thorough job. When you create a Filestream-enabled database, you can create a table with a VARBINARY(MAX) column and insert a BLOB using the technique we covered in a [previous post](https://andyspecht.github.io/2017-06-12-inserting-images/). However, instead of storing the file directly in SQL Server, SQL Server will store the file on your file system. For better or worse, your database backups and restores will include the images written to the filesystem. If this sounds interesting to you, check out [The Art of SQL Server FILESTREAM](http://assets.red-gate.com/community/books/art-of-ss-filestream.pdf). It is super thorough and readable.

Another suggestion is to use Content Addressable Storage. This is something I have no experience with, but check out [this post](https://www.brentozar.com/archive/2015/03/no-more-blobs/) from the Brent Ozar blog and the comments for some nice discussion of this solution.

That's enough for an overview of the issues with files and images in SQL Server. There's not really a perfect solution, but we have covered the directions worth exploring.