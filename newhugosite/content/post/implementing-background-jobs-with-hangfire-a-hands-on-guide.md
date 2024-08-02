+++
categories = ["Hangfire", "C#"]
date = 2024-07-01T20:36:57Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1547056961-3c25e9140b05?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3wxMTc3M3wwfDF8c2VhcmNofDN8fEJyaWNrJTIwd2FsbHxlbnwwfHx8fDE3MTk4NjYzNzl8MA&ixlib=rb-4.0.3&q=80&w=2000"
slug = "implementing-background-jobs-with-hangfire-a-hands-on-guide"
tags = ["Hangfire", "C#"]
title = "Hangfire Implementing Background Jobs"

+++


Hangfire is a robust library for managing background jobs in .NET applications, allowing developers to easily create and manage tasks that run asynchronously.

Whether you're scheduling recurring tasks, executing one-off jobs, or managing time-consuming operations without blocking the main thread, Hangfire provides a flexible and reliable solution. In this article, we'll walk through setting up Hangfire to automatically clean up expired JWT tokens from a database, ensuring your authentication system remains efficient and secure.

## Requirements

Before we dive into the implementation, make sure you have the following:

* At least .NET 6.0 SDK installed
* A basic understanding of .NET and C#
* A running SQL Server instance
* Visual Studio or any preferred C# IDE

## Setting Up the Project

Open your terminal or command prompt and run the following commands to create a new .NET web application:

```bash
dotnet new webapi -n HangfireBackgroundJob
cd HangfireBackgroundJob

```

### Install Hangfire

Add Hangfire and its SQL Server storage package to your project:

```bash
dotnet add package Hangfire
dotnet add package Hangfire.SqlServer

```

### Configure Hangfire

Open `Startup.cs` (or `Program.cs` if you're using the new minimal hosting model) and configure Hangfire in the `ConfigureServices` method:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Configure Hangfire to use SQL Server storage
    services.AddHangfire(configuration => 
        configuration.SetDataCompatibilityLevel(CompatibilityLevel.Version_170)
                     .UseSimpleAssemblyNameTypeSerializer()
                     .UseRecommendedSerializerSettings()
                     .UseSqlServerStorage("Server=your_server;Database=your_database;User Id=your_user;Password=your_password;", new SqlServerStorageOptions
                     {
                         CommandBatchMaxTimeout = TimeSpan.FromMinutes(5),
                         SlidingInvisibilityTimeout = TimeSpan.FromMinutes(5),
                         QueuePollInterval = TimeSpan.FromSeconds(15),
                         UseRecommendedIsolationLevel = true,
                         DisableGlobalLocks = true
                     }));

    services.AddHangfireServer();
}

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseRouting();
    app.UseAuthorization();

    // Use Hangfire Dashboard (optional for monitoring jobs)
    app.UseHangfireDashboard();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}

```

### Creating the Background Job

Now, let's create a background job that deletes expired JWT tokens from the database.

**Create a Service for Token Cleanup**

First, create a service that will handle the token cleanup logic. Add a new class called `TokenCleanupService.cs`:

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;
using Microsoft.EntityFrameworkCore;

public sealed class TokenCleanupService
{
    private readonly ApplicationDbContext _context;
    private readonly ILogger<TokenCleanupService> _logger;

    public TokenCleanupService(ApplicationDbContext context, ILogger<TokenCleanupService> logger)
    {
        _context = context;
        _logger = logger;
    }

    public async Task CleanUpExpiredTokensAsync()
    {
        var expiredTokens = await _context.JwtTokens
            .Where(t => t.ExpirationDate < DateTime.UtcNow)
            .ToListAsync();

        if (expiredTokens.Any())
        {
            _context.JwtTokens.RemoveRange(expiredTokens);
            await _context.SaveChangesAsync();
            _logger.LogInformation("Deleted {count} expired tokens.", expiredTokens.Count);
        }
    }
}

```

**Schedule the Background Job**

Open `Startup.cs` or `Program.cs` and schedule the background job using Hangfire:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env, IRecurringJobManager recurringJobManager)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseRouting();
    app.UseAuthorization();

    app.UseHangfireDashboard();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Schedule the token cleanup job to run daily at midnight
    recurringJobManager.AddOrUpdate(
        "TokenCleanup",
        () => new TokenCleanupService().CleanUpExpiredTokensAsync(),
        Cron.Daily);
}

```

### Running and Monitoring the Job

Start your application by running

```bash
dotnet run

```

Navigate to `http://localhost:5000/hangfire` to view the Hangfire Dashboard and monitor your jobs.

**Verify the Job Execution**

Check your database to ensure expired tokens are being deleted as expected. You can also view the job logs in the Hangfire Dashboard to verify successful execution.

## Conclusion

Using Hangfire to manage background jobs in a .NET application is a straightforward and efficient way to handle tasks that need to run asynchronously.

In this hands-on guide, we've set up a Hangfire project and created a background job to clean up expired JWT tokens from a database.

This ensures that your authentication system remains clean and performant without blocking your application's main thread. Hangfire's flexibility and ease of use make it an excellent choice for managing background tasks in any .NET application.

