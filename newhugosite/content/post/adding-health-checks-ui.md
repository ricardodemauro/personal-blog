+++
categories = [".NET 5", "HealthCheck", "DotNet Core", "devops", "C#", "en", "Monitoring", "Application Insights", "cloud", "container"]
date = 2020-09-21T18:17:00Z
description = ""
draft = false
image = "https://res-3.cloudinary.com/dgoa5llvh/image/upload/q_auto/health-check-ui-cover.png"
slug = "adding-health-checks-ui"
tags = [".NET 5", "HealthCheck", "DotNet Core", "devops", "C#", "en", "Monitoring", "Application Insights", "cloud", "container"]
title = "Adding Health Checks UI"

+++


This is the second article about **Health Checks** and **Application Monitoring**.

Health check by it self is very good feature. But Health Checks with a UI is much better, in fact is awesome!

1.  [Adding Health Check endpoint](__GHOST_URL__/adding-health-checks-to-net-core-application/)
2.  **Adding UI Health Check** [this article]
3.  [Endpoint Monitoring with Azure Application Insights](__GHOST_URL__/endpoint-monitoring-with-azure-application-insights/)
4.  Using Azure App Services Endpoint Monitoring

This article assumes that you already have Health Checks up and running. 
If not go back to [article](__GHOST_URL__/adding-health-checks-to-net-core-application/).

#### Before start 

The source code of the last article can be found at (https://github.com/ricardodemauro/Health-Check-Series) - **branch article-1**.

In the last blog post we discuss how to add health checks to your application returning JSON format. 

But would be nice to have some UI to easily check the status of your application.

> Remember *JSON* is machine friendly and *UI* is human friendly.

By the way, this configuration works for .NET and .Net CORE 3.1. Be happy!

#### Adding UI to our Health Checks
First add the dependency packages to our project.
- AspNetCore.HealthChecks.UI
- AspNetCore.HealthChecks.UI.Client
- AspNetCore.HealthChecks.UI.InMemory.Storage

Now let's register the dependencies.

```csharp
public void ConfigureServices(IServiceCollection services)
{
        //adding health check services to container
        services.AddHealthChecks()
                .AddMongoDb(mongodbConnectionString: _configuration.GetConnectionString("DefaultMongo"),
                        name: "mongo",
                        failureStatus: HealthStatus.Unhealthy); //adding MongoDb Health Check

        //adding healthchecks UI
        services.AddHealthChecksUI(opt =>
        {
                opt.SetEvaluationTimeInSeconds(15); //time in seconds between check
                opt.MaximumHistoryEntriesPerEndpoint(60); //maximum history of checks
                opt.SetApiMaxActiveRequests(1); //api requests concurrency
                
                opt.AddHealthCheckEndpoint("default api", "/healthz"); //map health check api
        })
        .AddInMemoryStorage();
}
```

And them add to the application pipeline.

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        //adding endpoint of health check for the health check ui in UI format
        endpoints.MapHealthChecks("/healthz", new HealthCheckOptions
        {
            Predicate = _ => true,
            ResponseWriter = UIResponseWriter.WriteHealthCheckUIResponse
        });

        //map healthcheck ui endpoing - default is /healthchecks-ui/
        endpoints.MapHealthChecksUI();

        endpoints.MapGet("/", async context => await context.Response.WriteAsync("Hello World!"));
    });
}
```
> `Startup.cs`

Note that we're adding another `Health Check API` endpoint called `/healthz` with a specific configuration, custom `ResponseWriter`.

This is will be used by the UI Health Check client (ajax call).

#### Wrap it up

Now build, run, and open the browser Url `http://{YOUR-SERVER}/healthchecks-ui`.

![health-check-ui-1](https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/health-check-ui-1.png)

#### Final toughts 

You can also customize the branding though CSS. For that I recommend going to the official website - [xabaril's site](https://github.com/xabaril/AspNetCore.Diagnostics.HealthChecks).

#### Source Code
The source code can be found at https://github.com/ricardodemauro/Health-Check-Series - **branch article-2**.

{{< bookmark url="__GHOST_URL__/endpoint-monitoring-with-azure-application-insights/" title="Endpoint Monitoring with Azure Application Insights" description="Let’s work with Application Insights - an Microsoft Azure service - to monitor our application through the endpoint /healthcheck.\nThis is the third article about Health Checks and Application Monitoring." icon="__GHOST_URL__/favicon.ico" author="Ricardo" publisher="rmauro.dev" thumbnail="https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/application-insights-cover-1.jpg" caption="Next of the series - Endpoint Monitoring with Azure Application Insights" >}}



