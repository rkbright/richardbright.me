---
layout:     post
title:      SAS SQL Pass-Through to Postgres 
date:       2021-09-06
summary:    Using SQL Pass-Through to build your database
permalink: /sas-sql-pass-through-postgres/
toc: true
tags:
  - sas
---

![SAS Logo](https://richardbright.me/images/postgres.png)

SQL pass-through is a quick and easy way to build your database from your SAS IDE. You can certainly use the SAS ``data step`` or ``proc sql`` procedure to build your database, this is just another way to build and/or query your database from SAS and interact directly with the database. 

SAS uses the SAS/ACCESS engine to make the connection. You can run the ``proc setinit; run;`` command to determine if your environment includes the Postgres engine. Look through the log fine for ``---SAS/ACCESS to Postgres``.

There are a few requirements for structuring your SAS code to connect to Postgres. 

1) your environment must have the SAS/ACCESS pass-through engine
2) you must use a ``connect`` and ``disconnect`` statement to make the connection to the database
3) use the ``execute`` statement to run the Postgres queries

Here is an example of the basic structure
```
proc sql noerrorstop noprint;
 connect to postgres as connectionName(server=serverName port=5432 
  user=userName password='###' database=databaseName;

execute (ALTER TABLE database.table ALTER COLUMN column_name TYPE varchar USING column_name::varchar(30); by exDB;

disconnect from exDB;
quit;
```

You can add multiple ``execute`` statements between the ``connection`` and ``disconnection`` statements. The SQL code within the ``execute`` statements will need to be Postgres's flavor of SQL. 

An added benefit is you are offloading the compute resources to the database where SAS is not having to facilitate the execution of the query between the database and the SAS runtime environment. This is helpful if you have a heavily used environment where compute capacity may be limited (let's just hope you don't upset the DBA by pushing large queries to the database.) 

Another advantage, SAS does not have to copy data out to execute queries in the SAS runtime environment. Queries and run locally in-database where the data currently resides and does not have to traverse the network. 

Hopefully this short snippet was helpful!