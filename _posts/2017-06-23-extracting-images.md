---
layout: post
title: Extracting Images and Files From SQL Server
image: ../img/ExtractingImage/vacuum.jpg 
tags: [SQL, SQL Server, BLOBs, Images]
---

[Last time](https://andyspecht.github.io/2017-06-12-inserting-images/) we looked at the fairly straightforward process of inserting images and file into SQL Server. Now, let's figure out how to get these things out of SQL Server and onto your filesystem.

It would be nice if you could just copy the BLOB into a text file, change the extension of the file, and voila you have a PDF or JPEG. It won't work. Instead, we need to use some .NET features to extract the BLOBs in a useable format. The most obvious vehicles for using these .NET features are C# and PowerShell, and we will use PowerShell, since it's something that anyone with SQL Server should have easy access to on their system.

### A PowerShell script for Extracting SQL Server BLOBs

Last time, we used this SQL to set up our test:


```sql
CREATE DATABASE chockerBoxoFiles;

USE chockerBoxoFiles;

CREATE TABLE theFiles
(
id INT IDENTITY(1,1) PRIMARY KEY,
fileName VARCHAR(200),
actualFile VARBINARY(MAX)
);

INSERT INTO
    theFiles(fileName,
      actualFile)
SELECT 'C:\Users\Andrew\Desktop\avatar-icon.png', 
   BulkColumn FROM OPENROWSET( 
      BULK 'C:\Users\Andrew\Desktop\avatar-icon.png', 
      SINGLE_BLOB) AS myfile;
```

So I'll assume we are working with this table structure, and we have at least one file in the database with a full filename. We will use a script that will restore the file to the location specified in our table (in our example C:\Users\Andrew\Desktop\avatar-icon.png).

Fortunately, there are already a few different PowerShell scripts that do almost exactly what we want to do. I'm using the one [here](https://social.technet.microsoft.com/wiki/contents/articles/890.export-sql-server-blob-data-with-powershell.aspx) as a template.

```powershell
$Server = "(LocalDb)\MSSQLLocalDB";         # SQL Server Instance.            
$Database = "chockerBoxoFiles";            
#$Dest = "C:\Users\Andrew\Desktop";             # Path to export to. Not necessary if file names have full location            
$bufferSize = 8192;               # Stream buffer size in bytes.  

# Select-Statement for name & blob            
# with filter.            
$Sql = "SELECT fileName
              ,actualFile
        FROM theFiles";   

# Open ADO.NET Connection            
$con = New-Object Data.SqlClient.SqlConnection;            
$con.ConnectionString = "Data Source=$Server;" +             
                        "Integrated Security=True;" +            
                        "Initial Catalog=$Database";            
$con.Open();  
       
         
# New Command and Reader            
$cmd = New-Object Data.SqlClient.SqlCommand $Sql, $con;            
$rd = $cmd.ExecuteReader();            
            
# Create a byte array for the stream.            
$out = [array]::CreateInstance('Byte', $bufferSize)            
            
# Looping through records            
While ($rd.Read())            
{    
    $fileLocation = $rd.GetString(0)        
    Write-Output ("Exporting: {0}" -f $rd.GetString(0));                    
    # New BinaryWriter            
    $fs = New-Object System.IO.FileStream $fileLocation,'Create','Write';           
    $bw = New-Object System.IO.BinaryWriter $fs;            
               
    $start = 0;            
    # Read first byte stream            
    $received = $rd.GetBytes(1, $start, $out, 0, $bufferSize - 1);            
    While ($received -gt 0)            
    {            
       $bw.Write($out, 0, $received);            
       $bw.Flush();            
       $start += $received;            
       # Read next byte stream            
       $received = $rd.GetBytes(1, $start, $out, 0, $bufferSize - 1);            
    }            
            
    $bw.Close();            
    $fs.Close();            
}            
            
# Closing & Disposing all objects            
$fs.Dispose();            
$rd.Close();            
$cmd.Dispose();            
$con.Close();            
            
Write-Output ("Finished");
```

The key feature we are using is the BinaryWriter. This is what is used to transform your SQL Server BLOB back into a file. 

It's handy to have a script like this in your pocket in case you need bulk-extract files from a database, and there is not a good export utility attached to the database's application. Now that we have the basics down, in the future I want to look at alternative ways of using a database to store images/file and the merits of various strategies.

