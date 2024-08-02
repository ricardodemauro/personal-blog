+++
categories = ["Serilog", "C#", ".NET 6", "DotNet"]
date = 2023-04-04T00:05:15Z
description = ""
draft = false
image = "https://res-1.cloudinary.com/dgoa5llvh/image/upload/q_auto/enricher-cover.webp"
slug = "serilog-custom-enricher-on-aspnet-core"
summary = "One of the key features of Serilog is the ability to Enrich Log Events with additional contextual information that can help in troubleshooting and debugging. \nIn this blog post, we will explore how to create a Custom Enricher with Serilog to add custom properties to log events."
tags = ["Serilog", "C#", ".NET 6", "DotNet"]
title = "Getting Started with Serilog Custom Enrichers"

+++


**Serilog** is a popular logging library for **.NET** that provides a flexible and extensible way to log messages from applications.

One of the key features of Serilog is the ability to **Enrich Log Events** with additional contextual information that can help troubleshoot and debug.

In this blog post, we will explore how to create a Custom Enricher with Serilog to add custom properties to log events.

## Development

To create a custom enricher with Serilog, we should define a class that implements the `ILogEventEnricher` interface.

```csharp
public interface ILogEventEnricher
{
    void Enrich(LogEvent logEvent, ILogEventPropertyFactory propertyFactory);
}
```

Let's start by creating a new class called `AspNetCoreEnvironmentEnricher` that implements the `ILogEventEnricher` interface:

```csharp
using Serilog.Core;
using Serilog.Events;
using System;

namespace Serilog.Labs.CustomEnrichers.Enrichers
{
    //class must implement interface ILogEventEnricher
    public class AspNetCoreEnvironmentEnricher : ILogEventEnricher
    {
        static readonly string _aspNetEnvironment;

        /// <summary>
        /// static constructor to load the environment variable
        /// </summary>
        static AspNetCoreEnvironmentEnricher()
        {
            _aspNetEnvironment = Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT");
        }

        public void Enrich(
        	LogEvent logEvent, 
            ILogEventPropertyFactory propertyFactory)
        {
            var enrichProperty = propertyFactory
            	.CreateProperty(
                	"AspNetCoreEnvironment", 
                    _aspNetEnvironment);

            logEvent.AddOrUpdateProperty(enrichProperty);
        }
    }
}

```

The `Enrich` method is called for each log event, and we can use it to add custom properties to the `logEvent` parameter. The `propertyFactory` parameter can be used to create new properties that we can add to the log event.

### Using the Serilog Enricher

Now that we have our custom enricher class, let's use it with Serilog. We can add it to the logger configuration like this:

```csharp
Log.Logger = new LoggerConfiguration()
    .Enrich.With(new AspNetCoreEnvironmentEnricher())
    .WriteTo.Console(
    	outputTemplate: "[{AspNetCoreEnvironment} {Level:u3}] {Message:lj}{NewLine}{Exception}")
    .CreateLogger();

```

In this example, we added our custom enricher to the logger configuration using the `Enrich.With` method. We passed the current user's name "Alice" to the constructor of our enricher.

Now, when we log a message, the `UserName` property will be added to the log event:

```csharp
Log.Information("Hello, world!");

/*Will Output
[Information Development] Hello, world!
*/
```

## 😎Conclusion

Creating a custom enricher with Serilog is a simple and powerful way to add contextual information to log events.

By implementing the `ILogEventEnricher` interface and using the `propertyFactory` parameter, we can add any custom properties we need to help in troubleshooting and debugging our applications.

