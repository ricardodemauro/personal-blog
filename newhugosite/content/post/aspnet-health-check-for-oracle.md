+++
categories = ["HealthCheck", ".NET 6", "C#", "Design Patterns"]
date = 2022-10-03T16:22:14Z
description = ""
draft = true
image = "https://images.unsplash.com/photo-1631543722888-01f8a17ebf3f?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwxMTc3M3wwfDF8c2VhcmNofDF8fHNhbml0eXxlbnwwfHx8fDE2NjQ4MTQxMzk&ixlib=rb-1.2.1&q=80&w=2000"
slug = "aspnet-health-check-for-oracle"
tags = ["HealthCheck", ".NET 6", "C#", "Design Patterns"]
title = "Health Check in .NET 6 C#"

+++


Since the new .NET 6 we have **HealthCheck** methods incorporated in the framework without the need of external packages.

In this new version we no longer need to add any external packages to set up the Health Check Monitoring Pattern.

## 🎉 In this Issue

* Explain Health Monitoring Pattern
* Basic Setup .NET 6
* Extras
* Oracle Database Check
* Elastic Search Check

## Explain Health Monitoring Pattern

### The Problem to be Solved

How we can detect if a service is running or unable to receive/handle requests?

## Solution

The service should expose an Health Check API endpoint (e.g. HTTP `/health`) that returns the health of the service. The API endpoint handler performs various checks, such as

* the status of the connections to the infrastructure services used by the service instance
* the status of the host, e.g. disk space
* application specific logic

Health checks are exposed by an app as HTTP endpoints. Health check endpoints can be configured for various real-time monitoring scenarios:

* Health probes can be used by container orchestrators and load balancers to check an app's status. For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container. A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.
* Use of memory, disk, and other physical server resources can be monitored for healthy status.
* Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.

Health checks are typically used with an external monitoring service or container orchestrator to check the status of an app. Before adding health checks to an app, decide on which monitoring system to use. The monitoring system dictates what types of health checks to create and how to configure their endpoints.

`Health Check` in C# ****.NET**** is very simple. With just a few lines of code, you can set up everything to monitor the ****Health of our Application****.



{{< figure src="http://res-4.cloudinary.com/dgoa5llvh/image/upload/q_auto/HealthCheck.drawio.png" caption="Monitoring Pattern" >}}

## Basic Set Up

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddHealthChecks();

var app = builder.Build();

app.MapHealthChecks("/healthz");

app.Run();
```

```csharp
string connString = "CONNECTION_STRING_HERE";
services.AddOracle(
	connectionString: connString,
    healthQuery: "SELECT 1 FROM DUAL",
    name: "ORA",
    failureStatus: HealthStatus.Unhealthy,
    tags: new[] { "db", "sql", "oracle" }
);
```



