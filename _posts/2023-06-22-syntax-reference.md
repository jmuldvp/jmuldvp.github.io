# Personal Sytnax Reference

This page is going to serve as my personal reference to syntax I've used for personal learning or projects. I may end up spliting this up later but right now, it will hold all references to syntax I've used.

## Docker

1. Used on host terminal to pull an image
```console
sudo docker pull <image name>
```

2. If not on Docker Desktop terminal, this can be used in the host terminal to launch a terminal session in a container...
```console
docker exec -u root -it <container id> /bin/bash
```

3. This changes the SA password to whatever you enter on the image that was pulled down and starts the container
```console
sudo docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong@Passw0rd>" \
   -p 1433:1433 --name sql1 --hostname sql1 \
   -d \
   mcr.microsoft.com/mssql/server:2022-latest
```

4. To view Docker containers
```console
sudo docker ps -a
```

5. To view the status of a container named sql1
```console
docker exec -t sql1 cat /var/opt/mssql/log/errorlog | grep connection
```

6. Use this to change the System Administrator password after running it the first time or any other time you need to. This is a BASH script.
```console
sudo docker exec -it sql1 /opt/mssql-tools/bin/sqlcmd \
-S localhost -U SA \
 -P "$(read -sp "Enter current SA password: "; echo "${REPLY}")" \
 -Q "ALTER LOGIN SA WITH PASSWORD=\"$(read -sp "Enter new SA password: "; echo "${REPLY}")\""
```

7. Once at a terminal prompt inside the container (see #2 above), this can be issued to start SQLCMD. You will be prompted for the SA password.
```console
/opt/mssql-tools/bin/sqlcmd -S localhost -U SA
```

8. Copy files from host system to Docker container. Specifically I used Powershell to issue these commands to copy .mdf, .ndf and .ldf files over so that I can have a real database to play with. You'll want to change the container name and the path to your liking.
```console
cd /Users/<user Windows id/name>/Downloads/<whatever directory you created>
docker cp . <your container name>:/var/opt/mssql/data/
```

Bash script to stop and remove a container named sql1
```console
sudo docker stop sql1
sudo docker rm sql1
```

## Terminal

To change the ownership of a directory to a specific user (in this case the user is mssql)
```console
chown mssql /home/mssql/
```


Remove a directory named foo (be very careful with this one)
```console
rm -rf /home/foo
```

Find out which version of Linux is running (run as root inside the container)
```console
cat /etc/*-release
```

Find out what user is running when entering the Docker container
```console
whoami
```

Find any file with specific name, file with an extention or any file not in a specified file path. The last still looks at that path but does not search in it.
```console
find . -name charges.sql
find . -name "*.js"
find -name "*.sql" -not -path "/mnt/c"
```

## TSQL

List databases on MSSQL Server
```tsql
SELECT
    name AS Name
    ,database_id AS Data_ID
    ,create_date AS Create_DT
FROM
    sys.databases;
```

Creates a database called TestDB
```tsql
CREATE DATABASE TestDB;
```

Example of attaching database files
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
