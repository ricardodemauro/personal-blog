+++
categories = ["DotNet", "Authentication", "OAuth", ".NET 6", ".NET 7", ".NET 8", "JWT", "C#"]
date = 2023-05-29T23:28:28Z
description = ""
draft = false
image = "https://res-1.cloudinary.com/dgoa5llvh/image/upload/q_auto/jwt-code-cover.jpg"
slug = "csharp-get-jwt-token-request"
summary = "This blog post will explore extracting a JWT token from incoming requests using C#. \n\nHow to Get JWT Token from Request using C#"
tags = ["DotNet", "Authentication", "OAuth", ".NET 6", ".NET 7", ".NET 8", "JWT", "C#"]
title = "C# Get JWT Token from Request .NET 6"

+++


This blog post will explore extracting a JWT token from incoming requests using C#.

We will explore two methods using Minimal APIs, but it's the same process for MVC Controllers.

JSON Web Tokens (JWT) have become famous for securing web applications and APIs. JWTs provide a way to transmit claims between parties securely and are widely used for authentication and authorization purposes.

> Check out this article how to set up JWT Authentication in your project:[https://rmauro.dev/jwt-authentication-with-csharp-dotnet/](__GHOST_URL__/jwt-authentication-with-csharp-dotnet/)

## Table of Contents

* [Method 1: Parsing using Request Context](#method-1-getting-jwt-from-authentication-context)
* [Method 2: Parsing directly from HTTP Headers](#method-2-parsing-jwt-token-from-http-headers)
* [Conclusion](#conclusion)

## Method 1: Getting JWT from Authentication Context

This is the best way because it doesn't rely on the HTTP Headers. It uses the Authentication process instead.

PS.: The Authentication pipeline should be processed at this time.

```csharp
using Microsoft.AspNetCore.Authentication;

app.MapGet("/jwt-token/context", async (HttpContext ctx) =>
{
	//get the access token from the HttpContext
    string token = await ctx.GetTokenAsync("access_token");

    return TypedResults.Ok(new { token = token });
});
```

## Method 2: Parsing JWT Token from HTTP Headers

To retrieve the JWT token from a request, we need to access the request headers and extract the value of the "Authorization" header.

Here's a second example of how to parse the JWT token from a request:

```csharp
app.MapGet("/jwt-token/headers", (HttpContext ctx) =>
{
    if (ctx.Request.Headers.TryGetValue("Authorization", out var headerAuth))
    {
        var jwtToken = headerAuth.First().Split(new[] { ' ' }, StringSplitOptions.RemoveEmptyEntries)[1];
        return Task.FromResult(
        	TypedResults.Ok(new { token = jwtToken })
        );
    }
    return Task.FromResult(
    	TypedResults.NotFound(new { message = "jwt not found" })
    );
});
```

### Source Code

[https://github.com/ricardodemauro/Labs.JwtAuthentication](https://github.com/ricardodemauro/Labs.JwtAuthentication)

## Conclusion

Extracting a JWT token from a request is a fundamental step in securing web applications and APIs.

Remember to handle error scenarios and follow best practices to ensure the security of your application.



