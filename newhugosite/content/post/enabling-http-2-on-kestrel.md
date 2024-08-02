+++
categories = ["C#", "https"]
date = 2020-09-16T22:16:16Z
description = ""
draft = true
image = "https://images.unsplash.com/photo-1544668637-89001ab6415e?ixlib=rb-1.2.1&q=80&fm=jpg&crop=entropy&cs=tinysrgb&w=2000&fit=max&ixid=eyJhcHBfaWQiOjExNzczfQ"
slug = "enabling-http-2-on-kestrel"
tags = ["C#", "https"]
title = "Simple Web Application with HTTP 2 over Kestrel"

+++


In this blog post we're going to create a simple web application with HTTP 2 enable on [Kestrel](https://docs.microsoft.com/pt-br/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-3.1).

Prerequisites to follow this article:

* OpenSSL for Windows - to generate a self signed certificate

First let generate a self signed SSL certificate. If you have an existing one you can skip this step.



On an existing .Net application open the `Program.cs` file.

```
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```



