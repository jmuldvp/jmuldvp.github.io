# Issue in connecting to MSSQL Docker Container from VS Code

I've reinstalled MSSQL Docker container a handful of times now checking out the different options and now I was ready to connect using VS Code.

- I made sure VS Code had the proper extensions (Docker and MSSQL).
- After clicking on the SQL Server button on the left panel of VS Code, it took me to a screen where I could add a connection.
- I added a connection and kept walking through the setup but I kept getting an error that I couldn't login.

The error I was getting was...
```text
mssql: Error: Unable to connect using the conneciton information provided.
```

When it was asking for the server information, I was keying in the following info. Note that I named my current testing container 'sql1'.

Server name: `.\sql1`

This is what I finally typed in to obtain a connection (which of course it then displayed the meta data content folders of Databases, Security and Server Objects. Either one of these values for a server name works.

Server name: `localhost\sql1,1433` ..or.. `.\sql1,1433`

Another window popped up within VS Code with a blue button that I clicked that read `Enable Trust Server Certificate`.

I was able to enter a simple script to query all existing databases and it worked.  When properly connected when having a script window opened up, there will be an execute button available at the top of the window like you would see in SSMS to execute a script.  When I did execute the script, it did ask me which connection I wanted to use and I chose the one I setup and it worked great.
