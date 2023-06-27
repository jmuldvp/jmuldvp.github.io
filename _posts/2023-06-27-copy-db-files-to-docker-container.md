# Copying MSSQL Files to Docker Container

Since I wanted more than the *master*, *model*, *msdb* and *tempdb* tables to tinker with, I did some Googling for how to copy database files to a Docker container. During that search I came across someone that explained how to do that with a set of 2013 StackOverflow database files. I will give the reference I found at the bottom of this post.

Steps I followed to obtain files:
- Downloaded the StackOverflow db files that were zipped up. The following commands are issued from the host system. Note, I ran this from PowerShell.
  - `cd /Users/<your Windows name/id>/Downloads`
  - `curl -o "so.7z" "http://downloads.brentozar.com.s3.amazonaws.com/StackOverflow2013_201809117.7z"`

Downloading this file took about an hour on my supposed cable 1GB internet connection. After downloading this file, unzip the files and place them into a directory all to their own. I use 7-zip and I won't explain the file unzipping process here.

This next set of steps assume you have a Docker container and it is already up and running. If you don't have one up and running, see a previous post of mine. Here are the next steps I followed:
- From the host system issue these commands...
  - `cd /Users/aaronbertrand/Downloads/<whatever directory you created here>`
  - `docker cp . <your container name>:/var/opt/mssql/data/`

The instructions I followed stated to run this script...

```tsql
CREATE DATABASE StackOverflow 
ON 
  (name = so1, filename = N'/var/opt/mssql/data/StackOverflow2013_1.mdf'),
  (name = so2, filename = N'/var/opt/mssql/data/StackOverflow2013_2.ndf'),
  (name = so3, filename = N'/var/opt/mssql/data/StackOverflow2013_3.ndf'),
  (name = so4, filename = N'/var/opt/mssql/data/StackOverflow2013_4.ndf')
LOG ON
  (name = solog, filename = N'/var/opt/mssql/data/StackOverflow2013_log.ldf')
FOR ATTACH;
```

When running the above script, I got the following error...

```text
Msg 3415, Level 16, State 2, Line 1
Database 'StackOverflow' cannot be upgraded because it is read-only, has read-only files or the user does not have permissions to modify some of the files. Make the database or files writeable, and rerun recovery.
```

Because of another issue I had, I knew that I needed to change the ownership of those 5 files. I issued the following command...

```console
chown mssql StackOverflow2013_1.mdf StackOverflow2013_2.ndf StackOverflow2013_3.ndf StackOverflow2013_4.ndf StackOverflow2013_log.ldf
```

After issuing the `chown` command, re-running the above TSQL statement was successful. I did get about 200 messages about upgrade steps since I brought this up on a newer database that started out like this...

```text
Converting database 'StackOverflow' from version 655 to the current version 957. 
Database 'StackOverflow' running the upgrade step from version 655 to version 668. 
Database 'StackOverflow' running the upgrade step from version 668 to version 669. 
Database 'StackOverflow' running the upgrade step from version 669 to version 670.
.
.
Total execution time: 00:00:02.602
```

After I saw that it was connected within VS Code, I ran this query...

```tsql
SELECT
  TOP 100 *
FROM
  StackOverflow.dbo.Badges
```

...and I successfully got results.

Here is the blog I followed to get this database running within my MSSQL Docker container...

[https://sqlblog.org/2020/03/15/attaching-restoring-databases-inside-a-container](https://sqlblog.org/2020/03/15/attaching-restoring-databases-inside-a-container)
