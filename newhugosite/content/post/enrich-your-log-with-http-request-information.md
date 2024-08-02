+++
date = 2023-04-04T19:28:05Z
description = ""
draft = true
image = "https://images.unsplash.com/photo-1624365168056-daf44387e2ae?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwxMTc3M3wwfDF8c2VhcmNofDIyfHxjb2luc3xlbnwwfHx8fDE2ODE2ODIzNjE&ixlib=rb-4.0.3&q=80&w=2000"
slug = "enrich-your-log-with-http-request-information"
title = "Enrich Serilog messages with Http Headers"

+++


**Serilog.AspNetCore** is a package that provides middleware to simplify the logging process for **.NET Web Applications**. It is built on top of **Serilog**, a popular logging framework that offers an efficient and flexible way to capture application events and generate logs for debugging, troubleshooting, and auditing.

One of the primary features of `Serilog` is the ability to enrich log events with contextual information of any kind, such as HTTP headers, that can help developers understand application behavior and diagnose issues more efficiently.

In this blog post, we will explore how to leverage `Serilog.AspNetCore` middleware to capture HTTP headers and include them in our logs. We will discuss how enriching our log messages with HTTP headers information can help us better understand our application's behavior and make troubleshooting more efficient.

## The Problem

When a web application logs a message, it typically includes basic information such as the log level, timestamp, and message text. However, this information may not be enough to understand what caused the issue. For example, if an error occurred in a web application, it may be challenging to determine the user's identity or the _URL_ they were trying to access at the time of the error.

To solve this problem, we need to enrich our logs with additional information. One way to do this is by adding **HTTP Headers** information to our log messages.

## Requirements

Before we can use _Serilog_ to enrich our logs with _HTTP Headers_ information, we need to make sure we have the required packages installed.

To get started, we need to install the following packages:

* Serilog
* Serilog.AspNetCore

These packages can be installed using the NuGet package manager or by adding the following package references to our project file:

{{< figure src="https://res-4.cloudinary.com/dgoa5llvh/image/upload/q_auto/serilog-dependencies.png" caption="Installed Packages" >}}

Or

```xml
<ItemGroup>
  <PackageReference Include="Serilog" Version="2.10.0" />
  <PackageReference Include="Serilog.AspNetCore" Version="4.1.0" />
</ItemGroup>
```

### Basic AspNet Project Setup

Here is a code sample with _Serilog_ enabled.

```csharp
using Serilog;

Log.Logger = new LoggerConfiguration()
    .WriteTo.Console()
    .CreateLogger();

try
{
    Log.Information("Starting web application");

    var builder = WebApplication.CreateBuilder(args);

    builder.Host.UseSerilog(); 
    
    var app = builder.Build();

    app.MapGet("/", () => "Hello World!");

    app.Run();
}
catch (Exception ex)
{
    Log.Fatal(ex, "Application terminated unexpectedly");
}
finally
{
    Log.CloseAndFlush();
}
```

## Adding Enricher Information

To enrich our logs with HTTP headers information using Serilog, we need to configure the Serilog middleware in our application. We can achieve this by using the `UseSerilogRequestLogging` extension method provided by Serilog.

In the code example at the top of this blog post, we can see an example of how to configure Serilog middleware to enrich our logs with HTTP headers information.

```csharp
app.UseSerilogRequestLogging(x =>
{
    x.EnrichDiagnosticContext = (diagnosticContext, httpContext) =>
    {
    	diagnosticContext.Set("RequestHost", httpContext.Request.Host.Value);
        diagnosticContext.Set("RequestScheme", httpContext.Request.Scheme);
        
        diagnosticContext.Set("ClientIp", httpContext.Connection.RemoteIpAddress);
        diagnosticContext.Set("ClientAgent", httpContext.Request.Headers.UserAgent);
        
        if (httpContext.Request.QueryString.HasValue)
        	diagnosticContext.Set("QueryString", httpContext.Request.QueryString.Value);
		
        // Set the content-type of the Response at this point
        diagnosticContext.Set("ContentType", httpContext.Response.ContentType);
	};
});
```

In the above code example, we're setting up the _Serilog_ middleware to log additional information such as the Request Host, Request Scheme, Client IP, Client Agent, and Query String (if present). We're also setting the Content Type of the response, which can be useful in identifying issues with the response content.

## Conclusion

In this blog post, we've explored how to enrich our logs with additional HTTP headers information using Serilog. By doing this, we can make our logs more informative and easier to understand, which can be invaluable when debugging issues in our web applications.

By setting up the Serilog middleware in our web application, we can quickly add HTTP headers information to our log messages, making it easier to understand what is happening inside our application. This can save developers time and effort, making it easier to identify and fix issues, ultimately resulting in a better experience for users.

