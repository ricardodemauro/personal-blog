+++
categories = ["C#", ".NET 6", "DotNet"]
date = 2022-10-06T14:29:39Z
description = ""
draft = true
image = "https://images.unsplash.com/photo-1508962914676-134849a727f0?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwxMTc3M3wwfDF8c2VhcmNofDR8fHdhdGNofGVufDB8fHx8MTY2NTA2NjYwOQ&ixlib=rb-1.2.1&q=80&w=2000"
slug = "simpleexecutiontracker"
tags = ["C#", ".NET 6", "DotNet"]
title = "Measure Code Execution Time with .NET C#"

+++


As a programmer, we may need to find out the execution time used by a particular segment of the C# code, in order to optimize the performance. For example, we may want to know how much time is taken for reading multiple files in the file system, or fetching data from the database, or executing some business logic.

> Provides a set of methods and properties that you can use to accurately measure elapsed time.

_Microsoft's Definition_

```csharp
using System;
using System.Diagnostics;

Stopwatch stopWatch = new Stopwatch();
stopWatch.Start();

for (int i = 0; i < 1000; i++)
{
   Console.Write(i);
}

stopWatch.Stop();

// Get the elapsed time as a TimeSpan value.
TimeSpan ts = stopWatch.Elapsed;

// Format and display the TimeSpan value.
string elapsedTime = string.Format(
	"{0:00}:{1:00}:{2:00}.{3:00}", 
    ts.Hours, 
    ts.Minutes, 
    ts.Seconds, 
    ts.Milliseconds / 10);
    
Console.WriteLine("RunTime " + elapsedTime);

```

C# includes the [Stopwatch](https://msdn.microsoft.com/en-us/library/system.diagnostics.stopwatch.aspx) class in the `System.Diagnostics` namespace, which can be used to accurately measure the time taken for code execution. You don't need to use DateTime and calculate the time manually.

The following example measures the time taken for the execution of the `for` loop using StopWatch.

```csharp
using System;
using System.Diagnostics;

namespace MyApp.Tracing
{
    public sealed class SimpleExecutionTracker : IExecutionTracker
    {
        long _startTimestamp;

        long _stopTimestamp;

        public void StartTracking()
        {
            _startTimestamp = Stopwatch.GetTimestamp();
        }

        public void StopTracking()
        {
            _stopTimestamp = Stopwatch.GetTimestamp();
        }

        public void Reset()
        {
            StartTracking();
            _stopTimestamp = 0;
        }

        public static SimpleExecutionTracker StartNew()
        {
            var t = new SimpleExecutionTracker();

            t.StartTracking();

            return t;
        }

        public TimeSpan ElapsedTime => TimeSpan.FromMilliseconds(GetElapsedMilliseconds(_startTimestamp, _stopTimestamp));

        public double Elapsed => GetElapsedMilliseconds(_startTimestamp, _stopTimestamp);

        static double GetElapsedMilliseconds(long start, long stop)
        {
            return (stop - start) * 1000 / (double)Stopwatch.Frequency;
        }
    }
}

```



