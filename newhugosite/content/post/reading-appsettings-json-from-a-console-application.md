+++
categories = ["DotNet", "C#"]
date = 2024-07-10T14:18:00Z
description = ""
draft = false
image = "https://res-2.cloudinary.com/dgoa5llvh/image/upload/q_auto/appsettings-cover.jpg"
slug = "reading-appsettings-json-from-a-console-application"
tags = ["DotNet", "C#"]
title = "Reading appsettings.json from a Console Application"

+++


When developing C# .NET applications, it's common to store configuration settings in a JSON file, typically named `appsettings.json`.

This approach is not only useful for ASP.NET applications but can also be effectively used in C# Console Applications.

Before we start, let's make sure to have:

* .NET SDK installed on your machine.
* A basic understanding of C# and .NET.

## 🚗 Step-by-Step Guide

First, create a new .NET console application.

```bash
dotnet new console -n ConfigDemo cd ConfigDemo
```

To read from **appsettings.json**, we need to add the `Microsoft.Extensions.Configuration` package.

Run the following command.

```bash
dotnet add package Microsoft.Extensions.Configuration 
dotnet add package Microsoft.Extensions.Configuration.Json 
dotnet add package Microsoft.Extensions.Configuration.Binder
```

In the root of your project, create a file named `appsettings.json` and add some configuration settings.

```json
{ 
  "ApplicationName": "ConfigDemo", 
  "Logging": { 
    "LogLevel": { 
      "Default": "Information", 
      "Microsoft": "Warning", 
      "Microsoft.Hosting.Lifetime": "Information" 
	} 
  } 
}
```

Let's modify the `Program.cs` file to read configurations from `appsettings.json`.

```csharp
using Microsoft.Extensions.Configuration; 

// Build configuration 
var basePath = System.IO.Directory.GetCurrentDirectory();
var configuration = new ConfigurationBuilder()
	.SetBasePath(basePath) 
	.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true) 	.Build(); 
    
// Read settings 
var appName = configuration["ApplicationName"]; 
var logLevelDefault = configuration["Logging:LogLevel:Default"]; 
var logLevelMicrosoft = configuration["Logging:LogLevel:Microsoft"]; 

Console.WriteLine($"Application Name: {appName}"); 
Console.WriteLine($"Default Log Level: {logLevelDefault}"); 
Console.WriteLine($"Microsoft Log Level: {logLevelMicrosoft}"); } } }
```

Run the application to see the configuration settings printed to the console.

```bash
dotnet run
```

You should see output similar to.

```raw
Application Name: ConfigDemo
Default Log Level: Information 
Microsoft Log Level: Warning
```

#### Conclusion

Using `appsettings.json` in console applications ensures consistency across different types of .NET applications and simplifies the management of configuration settings.

Happy coding! 😎

