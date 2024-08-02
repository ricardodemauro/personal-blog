+++
date = 2020-10-06T23:22:38Z
description = ""
draft = true
slug = "end-to-end-net-5-setting-up-mongo-db"
title = "End to end Application with .NET 5 - Part I"

+++


In this blog post let's work with the **brand new .NET 5** (witch is still under *release candidate* mode). 

During this tutorial we`re going to create a Web API with **.NET 5** integrated with **Mongo DB** (in Mongo Atlas Database as a Service Cloud) executing all CRUD operation (create, read, update and delete).

Let's work with:
- Setting up the Project
- Use configuration files (appsettings.json)
- Environment Variables - **ASPNETCORE_ENVIRONMENT**
- Two tier separation of concerns
- Use of Dependency Injection thought native container
- Controller Actions with attributes - [FromBody], [FromRoute]
- And API Tests with [Insominia](https://insomnia.rest/) (I'm trying to move away from Postman)

#### Microsoft Announces .NET 5 (not core anymore)
> Today, we’re announcing that the next release after .NET Core 3.0 will be .NET 5. This will be the next big release in the .NET family.
https://devblogs.microsoft.com/dotnet/introducing-net-5/

## Table Contents
- [Before we start - Setup Requirements](#setup-requirements)
- [Configuring MongoDB in the Mongo Atlas cloud](#registering-mongo-atlas)
- [Creating the ASP.NET 5 project](#creating-first-web-app)
- [Adding the configuration template](#adding-a-configuration-model)
- [Adding a CRUD operations service](#adding-crud-operations-service)
- [Adding a Controller](#add-a-controller)
- [Testing the Web API](#test-the-web-api)

## <a name="setup-requirements"></a> Before we start - Setup Requirements

* [.NET Core 5 SDK](https://dotnet.microsoft.com/download/dotnet/5.0)
* [Visual Studio Preview](https://visualstudio.microsoft.com/vs/preview/)

## <a name="registering-mongo-atlas"> </a> Configuring MongoDB in the Mongo Atlas Cloud

Access the [Mongo Atlas Clout](https://www.mongodb.com/cloud/atlas) at https://www.mongodb.com/cloud/atlas

If you already have a Mongo Atlas account you can skip to this [section](#setup-env)

Click on **Try free** if you do not have an account, or **Sign In** if you already have a Mongo Atlas account.
#### <a name="creating-account"></a> Creating an account to access Mongo Atlas
Fill in the basic data (email, first name, last name and password).
And activate the account by the received email (Welcome to MongoDB) by clicking on **Sign in**.

#### <a name="sign-in"> </a> If you already have an account - Sign In

Log in using your username and password.

### <a name="preparing-environmnet-mongo-atlas"> </a> Preparing the environment

The cluster is where the Mongo Db databases will be stored (we can create more than one). In our case, we will use the bank created by default when we created the first cluster.

1. Click on **Build a Cluster**.
2. Select the free cluster (**Starter Clusters**) and then click on **Create a cluster**.
3. In *Cloud Provider* select **Azure** and leave the pre-selected `Virginia (eastus2)` region selected.
4. For the other options, we will leave the default values.
5. Now click on **Create Cluster** and wait for the creation of the cluster to finish.

The blue top bar indicates the status of the cluster creation. It may take a while to finish.

#### <a name="about-the-dashboard"> </a> Overview about the Dashboard

On the left side we have the possible configuration options, such as *Database Access* and *Network Access*.

- `Clusters` is where we see general data about the cluster and is where we will get the *connection string*.
- `Database Access` is where we manage user (s) and password (s) to access the database (s).
- `Network Access` is where we manage firewall rules (we release IP).
- I leave other options for you to explore later.

### <a name="setup-env"></a> Preparing the environment for our application

#### Creating the first user

1. Click on **Database Access**.
2. Then click on **ADD NEW USER** on the right side of the screen (green button).
3. Use the following configuration:
```bash
> Username = students
> Password = students
> User privileges = read and write to any database
```
Finally click on **Add User**.
Users creation can take some time.

#### Setting up the firewall rule

1. First click on `Network Access`.
2. Then on `Add IP Address`.
3. Now in `Allow Access From Anywhere` (this allows access from any IP)
Note that we will use this for ease of access, but it is not recommended for production environments.
4. Finally click on **Confirm**.

This change may also take some time to take effect.

#### 3 - Collecting connection string

1. Click on `Clusters` on the left side menu.
2. Then click on the `CONNECT` button in the `SANDBOX` section.
3. In the open popup click on `Connect your application`.

![mongo-get-connection-string](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/mongo-get-connection-string.png)

4. In the `Driver` section select `C#/.NET` and in `version` select `2.5 or later`.
5. Copy `connectionString` to notepad and replace `<password>` with the password we created (students) and also replace` <username> `if it is not already filled out.

Result should look something like:

> mongodb+srv://students:students@cluster0-XXXXX.azure.mongodb.net/test?retryWrites=true&w=majority

Awesome! We have our database ready for the C# application. Now let's start creating the Web API.



