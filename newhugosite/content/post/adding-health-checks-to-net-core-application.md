+++
categories = [".NET 5", "C#", "HealthCheck", "MongoDb", "DotNet", "Monitoring", ".NET 6", "DotNet Core"]
date = 2021-04-25T17:14:00Z
description = ""
draft = false
image = "https://res-5.cloudinary.com/dgoa5llvh/image/upload/q_auto/health-check-cover.png"
slug = "adding-health-checks-to-net-core-application"
tags = [".NET 5", "C#", "HealthCheck", "MongoDb", "DotNet", "Monitoring", ".NET 6", "DotNet Core"]
title = "Health Checks on your ASP.NET Core Application"

+++


## Health Endpoint Monitoring pattern

`Health Check` in **.NET 5** is very simple. With just a few lines of code, you can set up everything to monitor the **Health of our Application**.

> Implement functional checks in an application that external tools can access through exposed endpoints at regular intervals. This can help to verify that applications and services are performing correctly.

Reference to: https://docs.microsoft.com/en-us/azure/architecture/patterns/health-endpoint-monitoring

## Introduction

In this series of posts, we walk through **Health Checks** and monitoring your web application / Web APIs.

1.  **How to Add Health Checks to ASP.Net Core Application** [this post]
2.  [Adding UI Health Check](__GHOST_URL__/adding-health-checks-ui/)
3.  [Endpoint Monitoring with Azure Application Insights](__GHOST_URL__/endpoint-monitoring-with-azure-application-insights/)
4.  Using Azure App Services Endpoint Monitoring

In this post, we'll add an endpoint to monitor our existing ASP.NET Core Application **health status**. 

We'll understand how it's pretty simple in .NET Core / .NET 5 Applications. 
With just a few lines of code, all the infrastructure is ready to display the `health status` of our ASP.NET Applications.

We can monitor services such as:

- Database services, such as *SQL Server*, *Oracle*, *MySql*, *MongoDB*, among others;
- External API connectivity - external URLs;
- Disk connectivity (read/write);
- Cache services, such as *Redis*, *Memcache*, among others;
- Custom implementations (something unique to you).

Anything you need is here. If you don't find an implementation that suits you, we can create our custom implementation.

## Adding a Basic Health Check to ASP.NET Services

First, modify the **ConfigureServices** method as described below. It will add the `HealthChecks` service to our *DI Container*.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    //adding health check services to container
    services.AddHealthChecks();
}
```
> `Startup.cs`

Second, add on **Configure** pipeline the *health check* endpoint.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
        if (env.IsDevelopment())
            app.UseDeveloperExceptionPage();

        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapGet("/", async context =>
            {
                await context.Response.WriteAsync("Hello World!");
            });

            endpoints.MapDefaultControllerRoute();

            endpoints.MapHealthChecks("/healthcheck");
        });
}
```

> `Startup.cs`

Build, run, and access the URL `http://{YOUR-URL}/healthcheck` you should see something like this.

![basic-health-check](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/basic-health-check.png)

All set and done to start adding services to the monitoring list. Now let's add one service to the monitor list - **Mongo Db**.

## Adding Mongo Db as a monitor service

Let's pick from *xabaril's* list. *xabaril's* Github repository contains a large number of `AspNetCore.Diagnostics.HealthChecks` packages ready for use.

- AspNetCore.HealthChecks.System
- AspNetCore.HealthChecks.Network
- AspNetCore.HealthChecks.SqlServer
- AspNetCore.HealthChecks.MongoDb
- AspNetCore.HealthChecks.Npgsql
- AspNetCore.HealthChecks.Elasticsearch
- AspNetCore.HealthChecks.Redis
- AspNetCore.HealthChecks.MySql

> https://github.com/xabaril/AspNetCore.Diagnostics.HealthChecks

Add the Nuget package `AspNetCore.HealthChecks.MongoDb` to your project and modify the `AddHealthChecks` method to include the **Mongo Db** Health Check.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    //adding health check services to container
    services.AddHealthChecks()
        .AddMongoDb(mongodbConnectionString: "YOUR-CONNECTION-STRING",
        name: "mongo", 
        failureStatus: HealthStatus.Unhealthy); //adding MongoDb Health Check
}
```

> `Startup.cs`

After that, we are good to start monitoring the Mongo Db connectivity. Let's run our application again and retest the health status page.

![basic-health-check](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/basic-health-check.png)

## Adding a second endpoint with more details

Let's create a second endpoint with more information about the status, such as which health check is failing.

```csharp
using System.Linq;
using Microsoft.Extensions.Diagnostics.HealthChecks;
using System.Text.Json;

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    /*existing code*/

    app.UseEndpoints(endpoints =>
    {
        /*existing code*/

        endpoints.MapHealthChecks("/healthcheck");

        endpoints.MapHealthChecks("/healthcheck-details",
            new HealthCheckOptions
            {
                ResponseWriter = async (context, report) =>
                {
                    var result = JsonSerializer.Serialize(
                        new
                        {
                            status = report.Status.ToString(),
                            monitors = report.Entries.Select(e => new { key = e.Key, value = Enum.GetName(typeof(HealthStatus), e.Value.Status) })
                        });
                    context.Response.ContentType = MediaTypeNames.Application.Json;
                    await context.Response.WriteAsync(result);
                }
            }
        );
    });
}
```

> `Startup.cs`

Run again and access the endpoints `/healthcheck-details`.

![second-endpoint-monitor](https://res-1.cloudinary.com/dgoa5llvh/image/upload/q_auto/v1/ghost-blog-images/second-endpoint-monitor.jpg)

Now you can customize the response of your Health Check Endpoint with anything you need.

In the next article, I'll show you how to put a good UI (user interface) to it.

## Source Code

https://github.com/ricardodemauro/HerosApi-Blog



