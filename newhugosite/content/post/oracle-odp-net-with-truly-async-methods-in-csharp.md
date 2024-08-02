+++
categories = ["oracle", "DotNet", "oracle-sql", ".NET 6", ".NET 7"]
date = 2023-08-24T18:01:18Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1561474119-1b76f3a79816?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3wxMTc3M3wwfDF8c2VhcmNofDR8fHBpbmd8ZW58MHx8fHwxNjkyOTAwMjE5fDA&ixlib=rb-4.0.3&q=80&w=2000"
slug = "oracle-odp-net-with-truly-async-methods-in-csharp"
tags = ["oracle", "DotNet", "oracle-sql", ".NET 6", ".NET 7"]
title = "Oracle ODP.NET with truly Async Methods in C#"

+++


Oracle's ODP.NET 23c Dev (or _Oracle.ManagedDataAccess.Core_) release brings genuine async/asynchronous methods to support to database interactions in C#, revolutionizing the way developers work with Oracle databases.

Under _PreRelease_ until today : 2023-08-24

{{< figure src="https://res-5.cloudinary.com/dgoa5llvh/image/upload/q_auto/oracle-pre-release.png" caption="Oracle.ManagedDataAccess.Core - pre release" >}}

## Async/Await in C#

Before delving into the specifics of ODP.NET 23c Dev for C#, let's take a moment to understand the power of asynchronous programming in C#.

The _async/await_ pattern enables developers to write non-blocking code, freeing the main thread to perform other tasks while asynchronous operations are in progress.

This results in improved responsiveness, smoother user experiences, and efficient utilization of system resources.

## Oracle ODP.NET 23c Dev: A New Era of Asynchronous Database Interaction

While previous versions of ODP.NET have _async_ methods, a closer inspection reveals that they were mere wrappers around `Task.FromResult` calls.

> These methods lacked true asynchronous behavior, limiting their potential in achieving optimal performance gains and making even worse then synchronous calls.

With the eagerly anticipated ODP.NET 23c Dev release, Oracle has taken a significant step forward by offering genuine _async/await_ support, transforming the landscape of database interactions.

{{< figure src="https://res-4.cloudinary.com/dgoa5llvh/image/upload/q_auto/oracle-pre-release-2.png" caption="Pre Release until today - 2023-08-24" >}}

The issue [#144](https://github.com/oracle/dotnet-db-samples/issues/211) on Github was open since November 2021. We're expecting this feature for a very long time.

{{< bookmark url="https://github.com/oracle/dotnet-db-samples/issues/144" title="Implement Async versions of Ado.Net methods · Issue #144 · oracle/dotnet-db-samples" description="Ado.Net classes such as DbCommand, DbConnection have async methods that aren’t truly async and just execute the non-async versions and return their result. It would be nice if ODP.NET had a real im…" icon="https://github.com/fluidicon.png" author="oracle" publisher="GitHub" thumbnail="https://opengraph.githubassets.com/f5b1f67795b059ff8e7ecaafef4bd0df58984503824c87940ba8b2d53f3f03c3/oracle/dotnet-db-samples/issues/144" caption="" >}}

## Key Async Methods in ODP.NET 23c Dev

Let's explore two pivotal _async_ methods that are now available in their true asynchronous glory.

* **OpenAsync**: Unlike its predecessor, the new `OpenAsync` method in ODP.NET 23c Dev truly opens a connection to the Oracle database asynchronously.
* **ExecuteReaderAsync**: This method allows developers to execute queries and retrieve data from the Oracle database asynchronously.

## Sample Async Database Interactions

Let's dive into some code examples that illustrate the difference between the old and the new:

```csharp
// Opening a connection asynchronously (previous version)
using Oracle.ManagedDataAccess.Client;
// ...

async Task OpenConnectionAsync(string connectionString)
{
    using var connection = new OracleConnection(connectionString);
    
    await connection.OpenAsync();
    Console.WriteLine("Connection opened asynchronously.");
}

// Executing a query and reading data asynchronously (new ODP.NET 23c Dev version)
using Oracle.ManagedDataAccess.Client;
// ...

async Task ExecuteQueryAsync(string connectionString, string query)
{
    using var connection = new OracleConnection(connectionString);
    
    await connection.OpenAsync();
    
    using var command = new OracleCommand(query, connection);
    using var reader = await command.ExecuteReaderAsync();
    
    while (await reader.ReadAsync())
    {
    	// Process data asynchronously
    }
}

```

## Embracing the Future: Leveraging ODP.NET 23c Dev for Superior Performance

The introduction of true asynchronous support in ODP.NET 23c Dev is a significant milestone for developers working with Oracle databases.

By embracing these new _async_ methods, you can create applications that are more responsive, efficient, and user-friendly.

As you transition to the latest version of ODP.NET, you'll unlock the full potential of asynchronous programming and usher in a new era of high-performance database interactions.

## References

* [Async/Await in C# - Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/async/)
* [Oracle Data Provider for .NET (ODP.NET) Documentation](https://docs.oracle.com/en/database/oracle/oracle-database/23/odpnt/featAsyncPipelining.html#GUID-27CA3823-92BA-489A-9356-9EAA7B86A4FE)

