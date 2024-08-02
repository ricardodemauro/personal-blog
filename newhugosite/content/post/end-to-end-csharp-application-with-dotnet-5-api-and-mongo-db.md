+++
categories = ["en", "C#", "MongoDb", "NoSql"]
date = 2020-10-04T01:49:44Z
description = ""
draft = true
slug = "end-to-end-csharp-application-with-dotnet-5-api-and-mongo-db"
tags = ["en", "C#", "MongoDb", "NoSql"]
title = "End to end C# Application with .NET 5 API and MongoDB"

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

## <a name="creating-first-web-app"> </a> Creating the ASP.NET Core Web API project

Open the **Visual Studio Preview**. The *Preview* version allows us to create projects in ASP.NET 5, since it's still under *release candidate mode*.

![Vs-Preview](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/Vs-Preview.png)

Create a new `ASP.NET Core Web Application` and name the project **MongoWebApi**.

In the next section select the new framework `ASP.NET Core 5` and the template `API`.

![vs-create-project](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/vs-create-project.png)

Add the `MongoDB.Driver` *NuGet* package to our project and we should be ready to start coding.

Your `CSPROJ` file should be similar to this.

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>net5.0</TargetFramework>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="MongoDB.Driver" Version="2.11.2" />
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
  </ItemGroup>
</Project>
```
> Note that `Swashbuckle.AspNetCore` was included by default during the project creation.

### Removing unnecessary content from our project

Remove the following files from your project.

- WeatherForecast.cs
- Controllers/WeatherForecastController.cs

![vs-removing-files](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/vs-removing-files.png)

### Adding the entity model to our project

First add a folder called *Models* to the project root. And then create a class named `Student` in the *Models* directory. 

Finally replace the content of the file created with the content below.

```csharp
using MongoDB.Bson;
using MongoDB.Bson.Serialization.Attributes;

namespace MongoWebApi.Models
{
    public class Student
    {
        [BsonId]
        [BsonRepresentation(BsonType.ObjectId)]
        public string Id { get; set; }

        [BsonElement("FullName")]
        public string Name { get; set; }

        public string Email { get; set; }
    }
}
 
```

In the previous class, the `Id` property:

* It is necessary to map the CLR (Common Language Runtime) object to the MongoDB collection.
* It is annotated with [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to be designated as the document's primary key.
* It is annotated with [[BsonRepresentation (BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as the type `string` instead an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm). Mongo handles the conversion from `string` to ʻObjectId`.
* The `Name` property is annotated with the [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm). The value of the `Name` attribute represents the name of the MongoDB collection property.

## <a name="adding-a-configuration-model"> </a> Adding the configuration template

First replace the content of the *appsettings.json* file with the one below:

```javascript line-numbers
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },

  "AllowedHosts": "*",

  "MongoDBSettings": {
    "CollectionName": "Students",
    "ConnectionString": "{VALUE COPIED IN NOTEPAD}",
    "DatabaseName": "StudentDb"
  }
}
```

Don't forget to replace the **{VALUE COPIED IN NOTEPAD}** with the actual value.

Now create the file `StudentDatabaseSettings.cs` in the *Models* directory with the following code:

```csharp line-numbers
namespace MongoWebApi.Models
{
    public class StudentDatabaseSettings : IDatabaseSettings
    {
        public string CollectionName { get; set; }

        public string ConnectionString { get; set; }

        public string DatabaseName { get; set; }
    }

    public interface IDatabaseSettings
    {
        string CollectionName { get; set; }

        string ConnectionString { get; set; }

        string DatabaseName { get; set; }
    }
}
```

The `StudentDatabaseSettings` class is used to store the **AlunosDatabaseSettings** properties of *appsettings.json* file.

Then add the code to `Startup.ConfigureServices` (Startup.cs file at the project root):

```csharp line-numbers
public void ConfigureServices(IServiceCollection services)
{
    //using MongoWebApi.Models;
    services.Configure<StudentDatabaseSettings>(Configuration.GetSection(nameof(StudentDatabaseSettings)));
    
    // requires using Microsoft.Extensions.Options
    services.AddSingleton<IDatabaseSettings>(sp => sp.GetRequiredService<IOptions<StudentDatabaseSettings>>().Value);
    
    //ommited code - do not delete
}
```

#### The previous code:

* The configuration instance to which the `StudentDatabaseSettings` section of the *appsettings.json* file is associated is registered in the DI (Dependency Injection) container.
* The `IDatabaseSettings` interface is registered in the DI with a service lifetime singleton. When inserted, the interface instance is resolved to an ʻStudentDatabaseSettings` object.

Now add the following code snippet at the top of *Startup.cs* to resolve the references `StudentsDatabaseSettings` and ʻIDatabaseSettings`:

```csharp line-numbers
using MongoWebApi.Models;
using Microsoft.Extensions.Options;
```

Build the solution to validate what we have so far. The output should be:

```bash line-numbers
Rebuild started...
1>------ Rebuild All started: Project: MongoWebApi, Configuration: Debug Any CPU ------
1>You are using a preview version of .NET. See: https://aka.ms/dotnet-core-preview
1>MongoWebApi -> C:\Users\****\source\repos\MongoWebApi\bin\Debug\net5.0\MongoWebApi.dll
========== Rebuild All: 1 succeeded, 0 failed, 0 skipped ==========
```

#### Structure of the project so far

![vs-structure-1](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/vs-structure-1.jpg)

## <a name="adding-crud-operations-service"> </a> Adding a CRUD operations service

1. Create a folder called *Services* at the root project.
2. Add a class called `StudentService` to the newly created *Services* directory with the following code:

```csharp line-numbers
using MongoDB.Driver;
using MongoWebApi.Models;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace MongoWebApi.Services
{
    public class StudentService
    {
        private readonly IMongoCollection<Student> _students;

        public StudentService(IDatabaseSettings settings)
        {
            var client = new MongoClient(settings.ConnectionString);
            var database = client.GetDatabase(settings.DatabaseName);

            _students = database.GetCollection<Student>(settings.CollectionName);
        }

        public Task<List<Student>> Get() => _students.Find(student => true).ToListAsync();

        public Task<Student> Get(string id) => _students.Find(student => student.Id == id).FirstOrDefaultAsync();

        public async Task<Student> Create(Student student)
        {
            await _students.InsertOneAsync(student);
            return student;
        }

        public async Task Update(string id, Student data)
            => await _students.ReplaceOneAsync(student => student.Id == id, data);

        public async Task Remove(Student data) => await _students.DeleteOneAsync(Student => Student.Id == data.Id);

        public async Task Remove(string id) => await _students.DeleteOneAsync(student => student.Id == id);
    }
}
```

#### The previous code:

In the previous code, an `IDatabaseSettings` instance is retrieved from the DI through constructor injection. This technique provides access to the *appsettings.json* configuration values that were added in the [Adding a configuration model](#adding-a-configuration-model).

The `StudentService` class uses the `MongoDB.Driver` members to perform all CRUD operations against the database:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) reads the server instance to perform database operations. The constructor for this class is provided in the MongoDB connection string information.
* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; is the proxy class with the database. 
Here we're using Generics to gain access to the documents [GetCollection\<\TDocument>(coleção)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm).

  * `collection` - it's the name of the collection;
  * `TDocument` - it's the type of document

`GetCollection<TDocument>(collection)` return an Object [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) of collection.

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) - deletes a document within a search criteria;
* [Find<\TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) - get all the documents in a given criteria;
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) - inserts a document in the collection;
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) - replaces a single document with a given criteria.

#### Keep up with the code

Now let's add StudentService to the DI container. To do so add the following code to `Startup.ConfigureServices`:

```csharp line-numbers
public void ConfigureServices(IServiceCollection services)
{
	//--existing code ommited

	//requires add using MongoWebApi.Services;
	services.AddTransient<StudentService>();
}
```

We have done a lot of coding so far. Build the solution to validate what we did. The output should be:

```bash line-numbers
Rebuild started...
1>------ Rebuild All started: Project: MongoWebApi, Configuration: Debug Any CPU ------
1>You are using a preview version of .NET. See: https://aka.ms/dotnet-core-preview
1>MongoWebApi -> C:\Users\****\source\repos\MongoWebApi\bin\Debug\net5.0\MongoWebApi.dll
========== Rebuild All: 1 succeeded, 0 failed, 0 skipped ==========
```

## <a name="add-a-controller"> </a> Adding a controller

Add an ʻAlunosController` class to the * Controllers * directory (if there is no folder, create one at the root of the project) with the following code:

```csharp line-numbers
using Microsoft.AspNetCore.Mvc;
using MongoWebApi.Models;
using MongoWebApi.Services;
using System;
using System.Threading.Tasks;

namespace MongoWebApi.Controllers
{
    public class StudentsController : ControllerBase
    {
        private readonly StudentService _service;

        public StudentsController(StudentService service)
        {
            _service = service ?? throw new ArgumentNullException(nameof(service));
        }

        [HttpGet]
        public async Task<IActionResult> Get() => Ok(await _service.Get());

        [HttpGet("{id:length(24)}", Name = "GetStudent")]
        public async Task<IActionResult> Get([FromRoute] string id)
        {
            var student = await _service.Get(id);

            if (student == null)
                return NotFound();

            return Ok(student);
        }

        [HttpPost]
        public async Task<IActionResult> Create([FromBody] Student student)
        {
            await _service.Create(student);

            return CreatedAtRoute("GetStudent", new { id = student.Id.ToString() }, student);
        }

        [HttpPut("{id:length(24)}")]
        public async Task<IActionResult> Update([FromRoute] string id, [FromBody] Student StudentIn)
        {
            if (!ModelState.IsValid)
                return BadRequest();

            var student = await _service.Get(id);

            if (student == null)
                return NotFound();

            await _service.Update(id, StudentIn);

            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public async Task<IActionResult> Delete([FromRoute] string id)
        {
            var student = await _service.Get(id);

            if (student == null)
                return NotFound();

            await _service.Remove(student.Id);

            return NoContent();
        }
    }
}
```

The Web API driver:

* Uses the class `StudentsService` to perform CRUD operations.
* Contains action methods to support HTTP GET, POST, PUT and DELETE requests.
* Calls `CreatedAtRoute` in the `Create` action method to return a response [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Status code 201 is the standard response to an HTTP POST method that creates a resource on the server. `CreatedAtRoute` also adds a` Location` header to the response. The `Location` header specifies the URI of the newly created book.

#### Structure of the project so far

![vs-structure-after-services](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/vs-structure-after-services.png)

## <a name="test-the-web-api"> </a> Testing the Web API

First ** compile ** and run our app. To do this, click on `Debug` in the top bar of Visual Code and then` Start Debugging`.
This takes a while on the first run. You can follow the build status through the terminal.

If it is your first time running a .Net application in Visual Code, you will be presented with the following window.

! [status-4-1] (https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/status-4-1.png)

Select the **. NET Core ** option. This will make Visual Code generate the necessary configuration files to debug the application.
Run `Debug` and` Start Debugging` again.

When ready, a browser window should automatically open with the address * https: // localhost: 5001 *.

> In the first run, you may be alerted about an untrusted digital certificate. You can accept the risk of the digital certificate. This is due to the Asp.Net Core * development time * digital certificate (not trusted in a production environment) (used only in ** Development time). So we can trust.

Now navigate to `https: // localhost: 5001 / api / students` to test the` GET` action method without parameters. The following JSON response should be displayed:

```json
   []
```


Perfect! This empty array return is expected because we don't have any records in Mongo Db (yet !!!).
This is great, we were able to validate the following items:

* Student Controller
* Reading the Configuration File
* Access to Mongo Db through Mongo Atlas

#### Running more elaborate tests

For more elaborate tests we will use the tool [Insomnia] (https://insomnia.rest/download/). Download and install it if you don't have it.

! [insomia] (https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/insomia.png)

You can also use other tools, such as POSTMan or ARC. In this case I decided on Insomia.

Save the content below with the name students-insomia.yaml. This file contains the GET / POST / PUT and DELETE tests (I made it ready to facilitate).

```javascript line-numbers
_type: export
__export_format: 4
__export_date: 2019-10-20T23:22:17.814Z
__export_source: insomnia.desktop.app:v7.0.3
resources:
  - _id: req_005b4fcbbf534bd68589fb228859c347
    authentication: {}
    body: {}
    created: 1571613715982
    description: ""
    headers: []
    isPrivate: false
    metaSortKey: -1571613582546.125
    method: DELETE
    modified: 1571613715982
    name: Delete Aluno
    parameters: []
    parentId: fld_fa1061f900604e57a83ac239a9dae413
    settingDisableRenderRequestBody: false
    settingEncodeUrl: true
    settingRebuildPath: true
    settingSendCookies: true
    settingStoreCookies: true
    url: https://localhost:5001/api/alunos/5daceb3f53f8fc3450886919
    _type: request
  - _id: fld_fa1061f900604e57a83ac239a9dae413
    created: 1571613715912
    description: ""
    environment: {}
    environmentPropertyOrder: null
    metaSortKey: -1571613617577
    modified: 1571613716019
    name: Alunos
    parentId: wrk_832b63dceba24667bbd70ddf633362fb
    _type: request_group
  - _id: wrk_832b63dceba24667bbd70ddf633362fb
    created: 1571527078826
    description: ""
    modified: 1571527078825
    name: Insomnia
    parentId: null
    _type: workspace
  - _id: req_fc5d374d60b342e0bb59434c4b810f2a
    authentication: {}
    body: {}
    created: 1571613715997
    description: ""
    headers: []
    isPrivate: false
    metaSortKey: -1571613582496.125
    method: GET
    modified: 1571613715997
    name: Get Aluno Por Id
    parameters: []
    parentId: fld_fa1061f900604e57a83ac239a9dae413
    settingDisableRenderRequestBody: false
    settingEncodeUrl: true
    settingRebuildPath: true
    settingSendCookies: true
    settingStoreCookies: true
    url: https://localhost:5001/api/alunos/5daceb3f53f8fc3450886919
    _type: request
  - _id: req_2320b4b3a6b54b01a2fa61c313e041a9
    authentication: {}
    body:
      mimeType: application/json
      text: |-
        {
            "id": "5daceb3f53f8fc3450886919",
            "nome": "Aluno 1 - Update",
            "email": "aluno1-update@faculdadeimpacta.com.br"
        }
    created: 1571613715964
    description: ""
    headers:
      - id: pair_a670675f0243435a9d3445694c7b58cb
        name: Content-Type
        value: application/json
    isPrivate: false
    metaSortKey: -1571613582446.125
    method: PUT
    modified: 1571613715964
    name: Update Aluno
    parameters: []
    parentId: fld_fa1061f900604e57a83ac239a9dae413
    settingDisableRenderRequestBody: false
    settingEncodeUrl: true
    settingRebuildPath: true
    settingSendCookies: true
    settingStoreCookies: true
    url: https://localhost:5001/api/alunos/5daceb3f53f8fc3450886919
    _type: request
  - _id: req_5dc593023227433bac88c0f00ee9851d
    authentication: {}
    body:
      mimeType: application/json
      text: |-
        {
        	"nome": "Aluno 1",
        	"email": "aluno1@faculdadeimpacta.com.br"
        }
    created: 1571613715946
    description: ""
    headers:
      - id: pair_d62363541a994b13b9566c92cd572b96
        name: Content-Type
        value: application/json
    isPrivate: false
    metaSortKey: -1571613582396.125
    method: POST
    modified: 1571613715946
    name: Create Aluno
    parameters: []
    parentId: fld_fa1061f900604e57a83ac239a9dae413
    settingDisableRenderRequestBody: false
    settingEncodeUrl: true
    settingRebuildPath: true
    settingSendCookies: true
    settingStoreCookies: true
    url: https://localhost:5001/api/alunos
    _type: request
  - _id: req_31184175d6b5455e86d2e9e0ec34e57e
    authentication: {}
    body: {}
    created: 1571613715929
    description: ""
    headers: []
    isPrivate: false
    metaSortKey: -1571613582346.125
    method: GET
    modified: 1571613715929
    name: Get Alunos
    parameters: []
    parentId: fld_fa1061f900604e57a83ac239a9dae413
    settingDisableRenderRequestBody: false
    settingEncodeUrl: true
    settingRebuildPath: true
    settingSendCookies: true
    settingStoreCookies: true
    url: https://localhost:5001/api/alunos
    _type: request
  - _id: env_7f3527ea80965a06126f1429c010d91e789910d7
    color: null
    created: 1571527079133
    data: {}
    dataPropertyOrder: null
    isPrivate: false
    metaSortKey: 1571527079133
    modified: 1571527079133
    name: Base Environment
    parentId: wrk_832b63dceba24667bbd70ddf633362fb
    _type: environment
  - _id: jar_7f3527ea80965a06126f1429c010d91e789910d7
    cookies: []
    created: 1571527079152
    modified: 1571527079152
    name: Default Jar
    parentId: wrk_832b63dceba24667bbd70ddf633362fb
    _type: cookie_jar
```
>alunos-insomia.yaml

Perfect, now click ** Import from file ** and import the file we just saved.

Make the first test, click on the item ** Get Students **, we should get the same empty array.

! [status-5] (https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/status-5.png)

> Again you can be alerted about * SSL Validation *. So we will also accept the alert by clicking on * Disable SSL Validation *, then in the * Network * section, deselect the item ** Validate certificates **.

Now test the other options, such as ** Create Student ** and then return to ** Get Students ** to see the created student.

Try using the ** Update Student ** (don't forget to update the route (URL) with the student's Id).

---

## <a name="next-steps"> </a> Next steps

To learn more about creating ASP.NET Core web APIs, check out the following resources:

* [Version of the original article on YouTube] (https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* Original article: [Microsoft Docs] (https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-mongo-app?view=aspnetcore-3.0)
* Include new templates like "Class", "Course", etc.



