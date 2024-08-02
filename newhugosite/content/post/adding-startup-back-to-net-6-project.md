+++
categories = [".NET 6", "C#", "en"]
date = 2022-06-05T14:20:46Z
description = ""
draft = false
image = "https://res-1.cloudinary.com/dgoa5llvh/image/upload/q_auto/startup-cover.webp"
slug = "adding-startup-back-to-net-6-project"
tags = [".NET 6", "C#", "en"]
title = "Adding Startup.cs back to .NET 6 Project"

+++


When _.NET 6_ was released, the first big change was the lack of **Startup.cs**. This is very nice and all. But sometimes we just want it back, and here is how.

First, create a new file called **Startup.cs** at the root of your project with the following content - don't forget the namespace.

```csharp
public class Startup
{
    readonly IConfiguration configuration;

    public Startup(IConfiguration configuration)
    {
        this.configuration = configuration ?? throw new ArgumentNullException(nameof(configuration));
    }

    public void ConfigureServices(IServiceCollection services)
    {
    	services.AddControllers();
    }

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapDefaultControllerRoute();
        });
    }
}
```

Then at **Program.cs** change to the following.

```csharp
//add necessary usings

var builder = WebApplication.CreateBuilder(args);

//create new instance of Startup
var startup = new Startup(builder.Configuration);

//configure all services
startup.ConfigureServices(builder.Services);

var app = builder.Build();

//configure the pipeline
startup.Configure(app, builder.Environment);

//run
app.Run();
```

The simpler the solution the better!

That is all folks.  ****[Subscribe](__GHOST_URL__/adding-startup-back-to-net-6-project/#/portal/signup)** ** to get notified on new posts.

