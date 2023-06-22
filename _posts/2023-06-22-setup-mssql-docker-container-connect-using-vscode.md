## My first note post - MSSQL/Docker

### Background
I've been a linux enthusiast for years. I was into making my machine dual boot Linux and Windows in the late 90's. I quit using the dual boot option and started using Microsoft Virtual Machine about 20 or more years ago and I'd install various Linux flavors just to play with different features. I then moved on to Oracle Virtual Box because Microsoft stopped development work on their virtual machine software.  I first heard about Docker about 5-6 years ago and knew that I needed to start learning that at some point.  Instead of going on a YouTube Docker learning spree, I am learning Docker as I go when I want to use it for various educational purposes.

I'm trying to run MS SQL Server 2022 in a Docker container and I'm trying to connect to it through VS Code and the MSSQL extension.  I know I could download the SQL Server 2022 dev edition along with SSMS and be on my way but I wanted to try something that was a little outside my comfort zone.  I also don't like how the SQL Server install (for Windows) puts things all over the place.  I'd like to keep SQL Server in one place and run it there.  I also don't want services to run in the background that don't need to be running and at the same time, I want all necessary processes to be running when I need them and I saw Docker as a way to do that.  

### Goal for my current personal project
Setup MS SQL 2022, connect to it with Visual Studio Code (MSSQL extension) and learn Docker as I go. I want the experience of using Docker (in case it ever comes in handy) and at the same time, I want to setup an MSSQL container to fill it with some public data to learn more about using Power BI and building out data models. I'm a beginner in every sense of the word when it comes to Power BI.

Here is my setup as of the time of this 'note'...
- **Hardware:** (Newer) Acer laptop with 16GB of RAM
- **OS:** Windows 11 Home Edition
- **Docker Desktop for Windows:** 4.20.1 (110738)
- **MS SQL image:** mcr.microsoft.com/mssql/server:2022-latest
- **Terminal:** Windows Subsystem for Linux (WSL) - Ubuntu 22.04.2 LTS
- **VS Code:** 1.79.2 (SQL Server (mssql) extension: 1.19.1)

**Note:** I chose the Ubuntu terminal because in a lot of the instructions I'm reading state that if I choose to use Powershell, I need to replace the single quotes with double quotes.  Plus, I'm more familiar with a linux terminal/shell than with a Powershell prompt.

### Error
When clicking on the Remote Explorer icon within VS Code, I can see the MSSQL container. When I right click on it and choose Attach in New Window, it looks like it is connecting and then an error appears...

```text
.
.
Command in container failed: mkdir -p /home/mssql/.vscode-server/bin/<insert long id code here that changes every time>
mkdir: cannot create directory '/home/mssql': Permission denied
Exit code 1
```

### Solution
What this told me was that /home/mssql folder needed to be created and that the right user needed to own it. I wasn't sure what user needed to own that directory. I tried to see what users existed on my WSL (host) shell and I didn't see anything that had to do with SQL. I finally found a post that made it clear to me that I needed to be inside the container to see this inforamtion.  I issued this command...

```console
docker exec -u root -it <container id> /bin/bash
```

...and I navigated to the /home directory and didn't see see an mssql directory.  I issued this command...

```console
cat /etc/passwd
```

...and saw an mssql user. I navigated to the /home directory and created an mssql directory...

```console
mkdir mssql
```

I knew I was partially there but not quite.  The mssql user needed to own that folder so I issued this command...

```console
chown mssql /home/mssql/
```

After I issued that command, I was able to connect to the container without error using VS Code.
