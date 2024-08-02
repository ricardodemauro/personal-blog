+++
categories = ["Docker", "oracle"]
date = 2022-05-30T17:15:12Z
description = ""
draft = false
image = "https://res-5.cloudinary.com/dgoa5llvh/image/upload/q_auto/oracle-database-cover.webp"
slug = "oracle-database-on-docker-for-development"
summary = "Lets set up an Oracle Database instance for development purpose using Docker is very easy. And it also runs in WSL2 over Windows.\r\n\r\nLet's use the oracleinanutshell/oracle-xe-11g image."
tags = ["Docker", "oracle"]
title = "Oracle Database on Docker for Development"

+++


Lets set up an _Oracle Database_ instance for development purpose using Docker is very easy. And it also runs in WSL2 over Windows.

Let's use the **oracleinanutshell/oracle-xe-11g** image. [https://hub.docker.com/r/oracleinanutshell/oracle-xe-11g](https://hub.docker.com/r/oracleinanutshell/oracle-xe-11g)

## Table Of Contents

* [Running Oracle Database On Docker](#running-oracle-database-on-docker)
* [Set Up a New User and Configure the Tablespace](#set-up-a-new-user-and-configure-the-tablespace)
* [Connect Using SQL Developer](#connect-using-sql-developer)

## Running Oracle Database on Docker

Run the following commands in your terminal.

```bash
#pulls the images from docker hub
docker pull oracleinanutshell/oracle-xe-11g

#runs the image using port forwarding 
docker run -d -p 49161:1521 oracleinanutshell/oracle-xe-11g

```

The output should be something like this.

{{< figure src="https://res-3.cloudinary.com/dgoa5llvh/image/upload/q_auto/v1/ghost-blog-images/docker-run.png" >}}

## Set Up a New User and Configure the Tablespace

With the Application running in the background let's create a new Oracle User and set up the appropriate tablespace and grants.

Get the running container Id and attach it running `bash`.

```bash
docker exec -it 82 bash
```

Type `sqlplus` to open SQLPlus in the terminal.

```bash
sqlplus
```

Connect using the _username_ and _password_ below.

```bash
Database Information

hostname: localhost
internal port: 49161
sid: xe
username: system
password: oracle

```

{{< figure src="https://res-5.cloudinary.com/dgoa5llvh/image/upload/q_auto/v1/ghost-blog-images/sqlplus.png" caption="Connected at SQLPlus" >}}

Run the following commands to set up the new user and tablespace.

```sql
CREATE TABLESPACE TSD_USERDB LOGGING DATAFILE 'TSD_USERDB.DBF' SIZE 200M AUTOEXTEND ON NEXT 200M MAXSIZE 400M;

CREATE TABLESPACE TSI_USERDB LOGGING DATAFILE 'TSI_USERDB.DBF' SIZE 200M AUTOEXTEND ON NEXT 50M MAXSIZE 400M;

CREATE USER USERDB IDENTIFIED BY PASSWORD DEFAULT TABLESPACE TSI_USERDB QUOTA UNLIMITED ON TSD_USERDB QUOTA UNLIMITED ON TSI_USERDB;


```

Then run the necessary grants.

```sql
GRANT CREATE SESSION TO USERDB;
GRANT CREATE PROCEDURE TO USERDB;
GRANT CREATE VIEW TO USERDB;
GRANT CREATE TABLE TO USERDB;
GRANT CREATE SEQUENCE TO USERDB;
GRANT CREATE TRIGGER TO USERDB;
```

## Connect Using SQL Developer

Connect using the recently created user to manage your database.

```bash
Database Information

hostname: localhost
port: 49161
sid: xe
username: USERDB
password: PASSWORD
```

{{< figure src="https://res-1.cloudinary.com/dgoa5llvh/image/upload/q_auto/v1/ghost-blog-images/sql-developer-connection.png" >}}

### Done!

