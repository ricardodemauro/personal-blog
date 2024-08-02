+++
categories = ["azure-functions", "Azure", "AZ204", "C#", "cloud", "en", "serverless"]
date = 2020-10-01T18:58:00Z
description = ""
draft = false
image = "https://res-3.cloudinary.com/dgoa5llvh/image/upload/q_auto/v1/ghost-blog-images/native-dependency-injection-azure-functions.jpg"
slug = "native-dependency-injection-in-azure-functions-with-csharp"
summary = "Adding Dependency Injection feature to Azure Functions without third part libraries."
tags = ["azure-functions", "Azure", "AZ204", "C#", "cloud", "en", "serverless"]
title = "Native Dependency Injection in Azure Functions with C#"

+++


C# Azure Functions support dependency injection through the native container.

This allows us to use the Dependency Inject principle in native Azure Functions. In this post, we're going to create a simple function to demonstrate the use of it.

## Introduction

> Azure Functions allows you to run small pieces of code (called "functions") without worrying about application infrastructure. With Azure Functions, the cloud infrastructure provides all the up-to-date servers you need to keep your application running at scale.[https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview)

For this demonstration, we're going to use this `RandomNumberService` to **Inject** into our function.

```csharp
using System;

namespace DependFunction.Services
{
    public interface IRandomNumber
    {
        int GetRandom();
    }

    public class RandomNumberService : IRandomNumber
    {
        static readonly Random _rand = new Random();

		//Get's a random number
        public int GetRandom() => _rand.Next(0, 10);
    }
}
```

## Required Dependencies

_Install/Update_ the following _NuGet_ packages in your project.

* Microsoft.Azure.Functions.Extensions
* Microsoft.NET.Sdk.Functions

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <!--ommited code-->
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.Functions.Extensions" Version="1.1.0" />
    <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="3.0.9" />
  </ItemGroup>
    <!--ommited code-->
</Project>

```

## Registering the Services in the Container

To register the services and we're going to create a _startup function_, just like we have in a common asp.net core application.

Create a file called `FunctionStartup.cs` at the root of your application.

{{< figure src="https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/func-solution-2.png" caption="Solution Explorer" >}}

Paste the following content into the file.

```csharp
using DependFunction.Services;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(DependFunction.Startup))]
namespace DependFunction
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddSingleton<IRandomNumber, NumberService>();
        }
    }
}
```

### Explanation

Before the namespace we're registering our class as a dependency start the function `[assembly: FunctionsStartup(typeof(DependFunction.Startup))]`. It will tell the function runtime to trigger our class before serving HTTP requests._Ps.: Do not use to process other things, only to register services._

Inheriting `FunctionStartup` we gain access to `IFunctionsHostBuilder` object where we can register all dependencies, such as _Scoped_, _Transient,_ or _Singleton_.

### Service Lifetime

* **Transient**: Transient services are created upon each request of the service.
* **Scoped**: The scoped service lifetime matches a function execution lifetime. Scoped services are created once per execution.
* **Singleton**: The singleton service lifetime matches the host lifetime and is reused across function executions on that instance.

### Using the service though Dependency Injection

Open your function file do the following changes

1. Remove `static` marker from the class and the function (we need to inject using constructors)
2. Create a constructor and inject the dependency _interface_

You should end with something like this:

```csharp
using System;
using DependFunction.Services;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace DependFunction
{
    public class Function1
    {
        private readonly IRandomNumber _randomService;

        public Function1(IRandomNumber randomService)
        {
            _randomService = randomService ?? throw new ArgumentNullException(nameof(randomService));
        }

        [FunctionName("Function1")]
        public IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            
            var result = new { random = _randomService.GetRandom() };

            return new OkObjectResult(result);
        }
    }
}

```

## Testing

Run the application and access the _URL_ for testing.

{{< figure src="https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/func-result.png" >}}

## Source Code

{{< bookmark url="https://github.com/ricardodemauro/az-functions-article" title="ricardodemauro/az-functions-article" description="Contribute to ricardodemauro/az-functions-article development by creating an account on GitHub." icon="https://github.githubassets.com/favicons/favicon.svg" author="ricardodemauro" publisher="GitHub" thumbnail="https://avatars0.githubusercontent.com/u/3687018?s=400&v=4" caption="Source Code of Azure Function" >}}

Hope you like it. Subscribe for more fresh content.

