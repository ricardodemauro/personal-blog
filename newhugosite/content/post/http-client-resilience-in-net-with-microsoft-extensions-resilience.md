+++
categories = ["Microsoft.Extensions.Resilience", "C#", "DotNet", "DotNet Core"]
date = 2024-07-05T01:57:30Z
description = ""
draft = true
image = "https://images.unsplash.com/photo-1568828668638-b1b4014d91a2?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3wxMTc3M3wwfDF8c2VhcmNofDV8fG51bWJlcnxlbnwwfHx8fDE3MjAxNDU5ODN8MA&ixlib=rb-4.0.3&q=80&w=2000"
slug = "http-client-resilience-in-net-with-microsoft-extensions-resilience"
tags = ["Microsoft.Extensions.Resilience", "C#", "DotNet", "DotNet Core"]
title = "HTTP Client Resilience in .NET with Microsoft.Extensions.Resilience"

+++


In this hands-on blog post, we'll explore how to use the `Microsoft.Extensions.Resilience` package to enhance the resilience of HTTP client requests in a .NET application.

## Table of Contents

1. Introduction
2. Prerequisites
3. Setting Up the Project
4. Configuring Resilience Policies
5. Testing the Resilience Strategies
6. Conclusion

## Introduction

As developers, we often encounter scenarios where network instability or temporary server issues can cause HTTP requests to fail. Instead of letting these failures disrupt the user experience, we can implement resilience strategies such as retries and circuit breakers.

The `Microsoft.Extensions.Resilience` package provides a straightforward way to integrate these strategies into your .NET applications. In this article, we'll walk through the steps to configure and use these resilience features in an HTTP client.

## Prerequisites

Before we begin, make sure you have the following installed:

* .NET 8 SDK
* An IDE such as Visual Studio or Visual Studio Code

## Setting Up the Project

Open your terminal or command prompt and run the following command to create a new ASP.NET Core Web API project.

```bash
dotnet new webapi -n ResilientHttpClientDemo
cd ResilientHttpClientDemo
```

Add the `Microsoft.Extensions.Resilience` and `Microsoft.Extensions.Http` packages to the project.

```bash
dotnet add package Microsoft.Extensions.Reilience
dotnet add package Microsoft.Extensions.Http
```

## Configuring Resilience Policies

Open the `Program.cs` file and configure your HTTP client to use resilience policies:

```csharp
using Microsoft.Extensions.DependencyInjection; 
using Microsoft.Extensions.Http.Resilience;

var builder = WebApplication.CreateBuilder(args); 

// Add HTTP client with resilience policies builder.Services.AddHttpClient("ResilientClient")
	.AddPolicyHandler(GetRetryPolicy())
    .AddPolicyHandler(GetCircuitBreakerPolicy()); 

var app = builder.Build(); 

app.MapGet("/", async (IHttpClientFactory httpClientFactory) => 
{ 
	var client = httpClientFactory.CreateClient("ResilientClient"); 
    var response = await client.GetAsync("https://api.example.com/data"); 
    
    return await response.Content.ReadAsStringAsync(); 
}); 

app.Run(); 

// Define the retry policy 
static IAsyncPolicy<HttpResponseMessage> GetRetryPolicy() 
{ 
	return HttpPolicyExtensions 
    	.HandleTransientHttpError() 
        .OrResult(msg => msg.StatusCode == HttpStatusCode.NotFound) 
        .WaitAndRetryAsync(
        	3, 
            retryAttempt => TimeSpan.FromSeconds(Math.Pow(2, retryAttempt))
		); 
} 

// Define the circuit breaker policy 
static IAsyncPolicy<HttpResponseMessage> GetCircuitBreakerPolicy() 
{ 
	return HttpPolicyExtensions 
    	.HandleTransientHttpError() 
        .CircuitBreakerAsync(5, TimeSpan.FromSeconds(30)); 
}
```

### Defined Policies

* **Retry Policy:** This policy retries the HTTP request up to 3 times with an exponential backoff (2, 4, 8 seconds) for transient errors or a 404 Not Found status.
* **Circuit Breaker Policy:** This policy breaks the circuit for 30 seconds if 5 consecutive transient errors occur, preventing further requests during this period to allow time for recovery.

## Testing the Resilience Strategies

To see these resilience strategies in action, you can create a simple endpoint that simulates transient faults. Add a new controller to your project:

**Create a New Controller**

In the `Controllers` folder, create a new file named `TestController.cs`:

‌ `using Microsoft.AspNetCore.Mvc; using System.Net; [ApiController] [Route("[controller]")] public class TestController : ControllerBase { private static int requestCount = 0; [HttpGet] public IActionResult Get() { requestCount++; // Simulate a transient fault if (requestCount % 3 != 0) { return StatusCode((int)HttpStatusCode.InternalServerError, "Transient error, please retry."); } return Ok("Request succeeded!"); } }`  csharp

* ‌

**Update the Client Request**

Modify the client request in `Program.cs` to call the new endpoint:

‌ `app.MapGet("/", async (IHttpClientFactory httpClientFactory) => { var client = httpClientFactory.CreateClient("ResilientClient"); var response = await client.GetAsync("http://localhost:5000/Test"); return await response.Content.ReadAsStringAsync(); });`  csharp

* ‌

**Run the Application**

Start your application by running:

‌ `dotnet run`  bash

* ‌

Visit `http://localhost:5000` in your browser. You should see that even though some requests initially fail, the retry policy eventually allows successful requests to be made. Additionally, if multiple consecutive failures occur, the circuit breaker will temporarily prevent further requests.

## Conclusion

In this hands-on article, we've explored how to enhance the resilience of HTTP client requests in a .NET application using the `Microsoft.Extensions.Resilience` package.

By implementing retry and circuit breaker policies, we've improved the robustness of our application, making it more tolerant of transient faults and ensuring a better user experience. These resilience strategies are essential tools for any developer looking to build reliable and fault-tolerant applications.

Stay tuned for more hands-on tutorials and tips on building resilient applications at [rmauro.dev](__GHOST_URL__/).

