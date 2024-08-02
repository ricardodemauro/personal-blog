+++
date = 2019-12-19T20:08:33Z
description = ""
draft = true
slug = "asp-net-core-with-mongodb-and-mongo-atlas-c"
title = "Asp.Net Core with MongoDb and Mongo Atlas - C#"

+++


This tutorial creates a Web API that performs CRUD (create, read, update, and delete) operations on a [MongoDB] NoSQL database (https://www.mongodb.com/what-is-mongodb).

Based on the article [Microsoft Docs] (https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-mongo-app?view=aspnetcore-3.0), created by [Pratik Khandelwal] (https: //twitter.com/K2Prk) and [Scott Addie] (https://twitter.com/Scott_Addie)
Modified by [Ricardo Mauro] (https://twitter.com/RicardoDeMauro)
Reading time: 18 minutes

In this tutorial you will learn how to:

> * Configure Mongo Atlas and MongoDb
> * Set username and password in Mongo Atlas
> * Perform MongoDB CRUD operations from an Asp.Net Core 3 Web API

## Prerequisites

* [.NET Core 3.0 SDK or later](https://www.microsoft.com/net/download/all)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [C# Extensions for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=jchannon.csharpextensions)

---

## <a name="registering-mongo-atlas"></a> Setting Up MongoDB in the Mongo Atlas Cloud

Access the [Mongo Atlas](https://www.mongodb.com/cloud/atlas) --https://www.mongodb.com/cloud/atlas.

Click `Try free` if you do not have an account, or `Sign In` if you already have a Mongo Atlas account.

#### <a name="creating-account"></a>Creating an Account for Mongo Atlas Access

Fill in the basic data (email, first name, last name and password).

And activate the account by the incoming email (Welcome to MongoDB) by clicking `Sign in`.

#### <a name="sign-in"></a>Sign In

Log in using your username and password.

### <a name="preparing-environmnet-mongo-atlas"></a>Preparing the Environment

The cluster is where the Mongo Db databases will be stored (we can create more than one). In our case we will use the database created by default when we create the first cluster.

1. Click on `Build a Cluster`.

2. Select the cluster free (`Starter Clusters`) and then click `Create a cluster`.

3. Under *Cloud Provider* select `Azure` and leave pre-selected region `Virginia(eastus2)`.

4. For the other options we will leave the default values.

5. Now click `Create Cluster` and wait for the cluster creation to finish.

The blue top bar indicates the status of cluster creation. It may take a while to finish.

#### <a name="little-context"></a>A Little Context - What do we see here?

On the left side we have possible configuration options such as *Database Access* and *Network Access*.

- `Clusters` is where we see general data about the cluster and where we will get the *connection string*.

- `Database Access` is where we manage username(s) and password(s) for access to database(s).

- `Network Access` is where we manage firewall rules (open for a givem IP address).

- Other options I'll leave for you to explore later.

### <a name="setup-env"></a>Preparing the Environment for Our Application

#### 1 - Creating the First User

1. Click on `Database Access`.

2. Then click on `ADD NEW USER` on the right side of the screen (green button).

3. Enter the following data:

```
> Username = students
> Password = students
> User privileges = read and write to any database
```

Finally click on `Add User`.

User creation may take some time.

#### 2 - Creating Firewall Rule

1. First click on `Network Access`.

2. Then on `ADD IP ADDRESS`.

3. Now in `ALLOW ACCESS FROM ANYWHERE` (this allows access from any IP)
Note that we will use this for ease of access, but it is not recommended for production environments.

4. Finally click `Confirm`.

This change may also take some time to take effect.

#### 3 - Collecting connection string (connectionString)

1. Click on `Clusters` in the left side menu.

2. Then click the `CONNECT` button in the` SANDBOX` session.

3. In the open popup click `Connect your application`.

![connect-cluster](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/connect-cluster.png)

4. In the `Driver` session select `C# / .NET` and in `version` select `2.5 or later`.

5. Copy `connectionString` to notepad and replace `<password>` with the password we created (students) and replace `<user>` if it is not already filled in.

Result should look something like:

> mongodb+srv://students:students@cluster0-XXXXX.azure.mongodb.net/test?retryWrites=true&w=majority

Great! We have our database ready for our application. We can now start building the Web API with ASP.NET Core.

## <a name="creating-first-web-app"></a>Creating the ASP.NET Core API Web Project

1. First create an empty folder called **MongoWebApi** within the **Documents** folder. This will be our place of work, in this folder will be deposited all our csharp source, configuration files, compiled (our dll's) and so on.

2. Now open the *



