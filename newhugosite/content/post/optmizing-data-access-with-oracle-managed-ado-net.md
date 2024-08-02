+++
categories = ["oracle", "C#", "100DaysOfCode"]
date = 2022-06-10T14:38:46Z
description = ""
draft = false
image = "https://res-2.cloudinary.com/dgoa5llvh/image/upload/q_auto/oracle-optminzing-cover.webp"
slug = "optmizing-data-access-with-oracle-managed-ado-net"
tags = ["oracle", "C#", "100DaysOfCode"]
title = "Optimizing Query Performance on C# ODP.NET - Oracle Managed Data Access"

+++


Oracle C# drive is already pretty fast with the default configuration. But we can always tweak it a little to make it even faster. By controlling the `FetchSize` property in `ODP.NET` we can make fewer _round trips_ to Oracle Database and fetch the data faster.

Let's take a look at how it's done and make your data access faster.

## Setup the Environment

Before we get started let's set up our environment for testing.

Set up the _Database_ with one _Table_ and **500 records**.

{{< bookmark url="__GHOST_URL__/oracle-database-on-docker-for-development/" title="Oracle Database on Docker for Development" description="Setting up Oracle Database for development in docker is very easy - it also runs in WSL2. Let’s use the oracleinanutshell/oracle-xe-11g image." icon="__GHOST_URL__/favicon.ico" author="Ricardo Mauro" publisher="rmauro.dev" thumbnail="https://res-5.cloudinary.com/dgoa5llvh/image/upload/q_auto/v1/ghost-blog-images/poster.jpg" caption="" >}}

### Our Baseline for measure - Code Without optimization

```csharp
static IEnumerable<int> LoadBigDataSet()
{
    List<int> dataColl = new(500);

    using var conn = new OracleConnection(ConnectionString);

    using var cmd = new OracleCommand(Query, conn);

    cmd.Connection.Open();

    using var reader = cmd.ExecuteReader();

    while (reader.Read())
    {
        int data = reader.GetInt32("ID_USER");

        dataColl.Add(data);
    }

    cmd.Connection.Close();

    return dataColl;
}
```

{{< figure src="https://res-2.cloudinary.com/dgoa5llvh/image/upload/q_auto/v1/ghost-blog-images/oracle-pre-performance.png" caption="Our Baseline - Code without optmization" >}}

In our baseline we had 3388 records returned in 06.35 seconds with single a column returned.

## Our Optimization - Controlling the Row Size Property

Controlling the amount of data fetched **per database round trip** can optimize application performance. It is inefficient to retrieve five rows of data per round trip when the end-user needs to use ten rows.

ODP.NET provides the query row size via the `RowSize` property, allowing us to specify a `FetchSize` using a set number of rows to be retrieved per round trip. This feature makes optimizing data retrieval much simpler for .NET programmers.

```csharp
static IEnumerable<int> LoadBigDataSetWithRowSize()
{
    List<int> dataColl = new(500);

    using var conn = new OracleConnection(ConnectionString);

    using var cmd = new OracleCommand(Query, conn);

    cmd.Connection.Open();

    using var reader = cmd.ExecuteReader();
    
    //double the fetch size
    reader.FetchSize = reader.FetchSize * 2;

    while (reader.Read())
    {
        int data = reader.GetInt32("ID_USER");

        dataColl.Add(data);
    }

    cmd.Connection.Close();

    return dataColl;
}
```

{{< figure src="https://res-3.cloudinary.com/dgoa5llvh/image/upload/q_auto/v1/ghost-blog-images/oracle-pos-performance.png" >}}

Wow! Now we have 3388 records returned in 05.24 seconds. We gained almost **1 second** in response time.

### Explanation

We've doubled the number of records returned by round trip. Of course, we can tweak a little more if you want.

All done here. If you like it **Subscribe** for more.

## Full Sample Code

```csharp
using Oracle.ManagedDataAccess.Client;
using System.Data;
using System.Diagnostics;

namespace OraclePerformance.ConsoleApp
{
    internal class Program
    {
        const string ConnectionString = @"CONNECTION_STRING_HERE";

        const string Query = @"SELECT * FROM TABLE WHERE DELETED = 0 
            UNION ALL SELECT * FROM TABLE WHERE DELETED = 0 
            UNION ALL SELECT * FROM TABLE WHERE DELETED = 0 
            UNION ALL SELECT * FROM TABLE WHERE DELETED = 0
            UNION ALL SELECT * FROM TABLE WHERE DELETED = 0
            UNION ALL SELECT * FROM TABLE WHERE DELETED = 0
            UNION ALL SELECT * FROM TABLE WHERE DELETED = 0
            UNION ALL SELECT * FROM TABLE WHERE DELETED = 0
            UNION ALL SELECT * FROM TABLE WHERE DELETED = 0
            UNION ALL SELECT * FROM TABLE WHERE DELETED = 0
            UNION ALL SELECT * FROM TABLE WHERE DELETED = 0";

        static void Main(string[] args)
        {
            Console.WriteLine("Hello, World!");

            Stopwatch watch = new Stopwatch();
            watch.Reset();

            watch.Start();

            var records = LoadBigDataSetWithRowSize();
            //var records = LoadBigDataSet();

            watch.Stop();

            Console.WriteLine("Ellapsed {0} with total records of {1}", watch.Elapsed, records.Count());

            Console.ReadKey();
        }

        static IEnumerable<int> LoadBigDataSet()
        {
            List<int> dataColl = new(500);

            using var conn = new OracleConnection(ConnectionString);

            using var cmd = new OracleCommand(Query, conn);

            cmd.Connection.Open();

            using var reader = cmd.ExecuteReader();

            while (reader.Read())
            {
                int data = reader.GetInt32("ID_USER");

                dataColl.Add(data);
            }

            cmd.Connection.Close();

            return dataColl;
        }

        static IEnumerable<int> LoadBigDataSetWithRowSize()
        {
            List<int> dataColl = new(500);

            using var conn = new OracleConnection(ConnectionString);

            using var cmd = new OracleCommand(Query, conn);

            cmd.Connection.Open();

            using var reader = cmd.ExecuteReader();

            reader.FetchSize = reader.FetchSize * 2;

            while (reader.Read())
            {
                int data = reader.GetInt32("ID_USER");

                dataColl.Add(data);
            }

            cmd.Connection.Close();

            return dataColl;
        }
    }
}
```

## ❤️ Enjoy this article?

**Forward to a friend** and let them know.Leave a comment with questions or improvements.



