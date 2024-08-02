+++
categories = [".NET 5", "DotNet Core", "C#", "Authentication", "Authorization", "en"]
date = 2021-03-28T21:43:45Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1586864387634-2f33030dab41?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwxMTc3M3wwfDF8c2VhcmNofDR8fGxvY2t8ZW58MHx8fHwxNjgxMjMyNTIy&ixlib=rb-4.0.3&q=80&w=2000"
slug = "api-key-authentication-extending-the-native-implementation"
tags = [".NET 5", "DotNet Core", "C#", "Authentication", "Authorization", "en"]
title = "API Key Authentication - Extending the native implementation"

+++


In this article, we're going to create the code (and understand how it works) to handle API Key authentication with just three lines of code extending the native Authentication mechanism.We want a simple and stupid solution and not some crazy implementation using _MVC_  `[Attributes]` or any customized _middleware_ to handle the Authentication.

```csharp
services.AddAuthentication(ApiKeyAuthNDefaults.SchemaName)
    .AddApiKey(opt => //here is our handler
    {
        opt.ApiKey = "Hello-World";
        opt.QueryStringKey = "key";
    });
```

Ok, ok, ok. I know it's hard to find a good implementation of API Key Authentication out there on the internet. I think it's also hard to ourself's needing of API Key Authentication on daily basis.But now you found it now! Hope you like it. Leave a comment :)

**Disclaimer:** Maybe I'm writing this article mad with someone hahahahaha. Please forgive me.

## Introduction

The native implementation of ASP.NET Authentication allows us to extend it and create our validation logic. With the `AddScheme` builder, we're going to implement the APIKey Authentication.

Everything begins with the `services.AddAuthentication` code. This builder provides us the ability to use the method `AddScheme`. Here is where our Auth ApiKey handler goes.

## Starting with the Code

Let's start by creating the file `ApiKeyAuthNOptions.cs`. This file will contain all configurations of our `ApiKeyAuthN` service, such as the `QueryStringKey` and `ApiKey`.

```csharp
using Microsoft.AspNetCore.Authentication;

namespace APIAuthentication.Resource.Infrastructure
{
    public class ApiKeyAuthNOptions : AuthenticationSchemeOptions
    {
        public string ApiKey { get; set; }

        public string QueryStringKey { get; set; }
    }
}

```

The second step is the file `ApiKeyAuthN.cs` __ with the following content.

```csharp
using Microsoft.AspNetCore.Authentication;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Options;
using System.Text.Encodings.Web;
using System.Threading.Tasks;

namespace APIAuthentication.Resource.Infrastructure
{
    public static class ApiKeyAuthNDefaults
    {
        public const string SchemaName = "ApiKey";
    }

    public class ApiKeyAuthN : AuthenticationHandler<ApiKeyAuthNOptions>
    {
        public ApiKeyAuthN(
            IOptionsMonitor<ApiKeyAuthNOptions> options, 
            ILoggerFactory logger, 
            UrlEncoder encoder, 
            ISystemClock clock) 
            : base(options, logger, encoder, clock)
        {
        }

        protected override Task<AuthenticateResult> HandleAuthenticateAsync()
        {
            throw new System.NotImplementedException();
        }
    }
}
```

The class `AuthenticationHandler` is responsible for making the validation and create the Authentication Ticket for the user.

I think you can guess where to put the validation logic, right? Here is the implementation.

```csharp
protected override Task<AuthenticateResult> HandleAuthenticateAsync()
{
    var apiKey = ParseApiKey(); // handles parsing QueryString

    if (string.IsNullOrEmpty(apiKey)) //no key was provided - return NoResult
        return Task.FromResult(AuthenticateResult.NoResult());

    if (string.Compare(apiKey, Options.ApiKey, StringComparison.Ordinal) == 0)
    {
        var principal = BuildPrincipal(Scheme.Name, Options.ApiKey, Options.ClaimsIssuer ?? "ApiKey");
        
        return Task.FromResult(AuthenticateResult.Success(new AuthenticationTicket(principal, Scheme.Name))); //Success. Key matched
    }

    return Task.FromResult(AuthenticateResult.Fail($"Invalid API Key provided.")); //Wrong key was provided
}
```

```csharp
protected string ParseApiKey()
{	
    if (Request.Query.TryGetValue(Options.QueryStringKey, out var value))
        return value.FirstOrDefault();

    return string.Empty;
}
```

```csharp
static ClaimsPrincipal BuildPrincipal(
	string schemeName, 
    string name, 
    string issuer, 
    params Claim[] claims)
{
    var identity = new ClaimsIdentity(schemeName);

    identity.AddClaim(new Claim(ClaimTypes.NameIdentifier, name, ClaimValueTypes.String, issuer));
    identity.AddClaim(new Claim(ClaimTypes.Name, name, ClaimValueTypes.String, issuer));

    identity.AddClaims(claims);

    var principal = new ClaimsPrincipal(identity);
    return principal;
}
```

The implementation of `BuildPrincipal` is up to you. You should customize the ClaimsIdentity with the Claims you find necessary in your application, such as _Role, PhoneNumber, Issuer, Partner Id,_ among others.

## Wrapping thing up - We're almost there

We have everything we need to start the authentication. Open your `Startup.cs` file and add the following contents.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(ApiKeyAuthNDefaults.SchemaName)
        .AddScheme<ApiKeyAuthNOptions, ApiKeyAuthN>(ApiKeyAuthNDefaults.SchemaName, opt =>
        {
            opt.ApiKey = "Hello-World";
            opt.QueryStringKey = "key";
            opt.ClaimsIssuer = "API-Issuer";
        });

    services.AddAuthorization();
}
```

In `AddScheme` we're configuring the _service_ to use our **Authentication handler**. Next set up the `Configure` method to use **Authentication** and **Authorization** middlewares.

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
        app.UseDeveloperExceptionPage();

    app.UseRouting();

    app.UseAuthentication(); //adds authentication middleware
    app.UseAuthorization(); //adds authorization middleware

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGet("/", async context =>
        {
            await context.Response.WriteAsync($"Hello World!{Environment.NewLine}");
            await WriteClaims(context);

        }).RequireAuthorization(); //forces user to be authenticated

        endpoints.MapGet("/anonymous", async context =>
        {
            await context.Response.WriteAsync($"Hello World!{Environment.NewLine}");
            await WriteClaims(context);
        }); //allow anonymous
    });
}

static async Task WriteClaims(HttpContext context)
{
    if (context.User.Identity.IsAuthenticated)
    {
        await context.Response.WriteAsync($"Hello {context.User.Identity.Name}!{Environment.NewLine}");

        foreach (var item in context.User.Identities.First().Claims)
        {
            await context.Response.WriteAsync($"Claim {item.Issuer} {item.Type} {item.Value}{Environment.NewLine}");
        }
    }
}
```

We also added `WriteClaims` method to see the user's _Claims_.

## Let's run it.

Without API Key

{{< figure src="https://res-2.cloudinary.com/dgoa5llvh/image/upload/q_auto/API-Key-Failed-To-Load.png" caption="Failed to load without API Key" >}}

With API Key added

{{< figure src="https://res-5.cloudinary.com/dgoa5llvh/image/upload/q_auto/API-Key-Show-Claims.png" caption="Claims of API Key" >}}

## Making it easier to use

Let's create an extension method builder for our AddApiKey handler.Create the file `ApiKeyAuthNExtensions.cs` with the following contents.

```csharp
using APIAuthentication.Resource.Infrastructure;
using System;

namespace Microsoft.AspNetCore.Authentication
{
    public static class ApiKeyAuthNExtensions
    {
        public static AuthenticationBuilder AddApiKey(this AuthenticationBuilder builder, Action<ApiKeyAuthNOptions>? configureOptions)
            => AddApiKey(builder, ApiKeyAuthNDefaults.SchemaName, configureOptions);

        public static AuthenticationBuilder AddApiKey(this AuthenticationBuilder builder, string authenticationScheme, Action<ApiKeyAuthNOptions>? configureOptions)
            => builder.AddScheme<ApiKeyAuthNOptions, ApiKeyAuthN>(authenticationScheme, configureOptions);
    }
}

```

This adds the extension method `AddApiKey` instead of calling `AddScheme`.Change the `Configure` method in `Startup` class to use the new method.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(ApiKeyAuthNDefaults.SchemaName)
        .AddApiKey(opt =>
        {
            opt.ApiKey = "Hello-World";
            opt.QueryStringKey = "key";
        }); //new version

    //.AddScheme<ApiKeyAuthNOptions, ApiKeyAuthN>(ApiKeyAuthNDefaults.SchemaName, opt =>
    //{
    //    opt.ApiKey = "Hello-World";
    //    opt.QueryStringKey = "key";
    //}); //old version

    services.AddAuthorization();
}
```

This is it! Hope you like it. Leave a comment.

**Source Code**

{{< bookmark url="https://github.com/ricardodemauro/article-APIAuthentication" title="ricardodemauro/article-APIAuthentication" description="Contribute to ricardodemauro/article-APIAuthentication development by creating an account on GitHub." icon="https://github.githubassets.com/favicons/favicon.svg" author="ricardodemauro" publisher="GitHub" thumbnail="https://avatars.githubusercontent.com/u/3687018?s=400&v=4" caption="source code at github" >}}



Disclaimer: There is a good implementation in the format of **nuget** package here: [https://github.com/mihirdilip/aspnetcore-authentication-apikey](https://github.com/mihirdilip/aspnetcore-authentication-apikey).

