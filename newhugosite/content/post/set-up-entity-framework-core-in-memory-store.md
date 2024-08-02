+++
categories = ["Entity Framework Core", "C#", "DotNet", "DotNet Core"]
date = 2024-07-05T02:30:13Z
description = ""
draft = false
image = "https://res-5.cloudinary.com/dgoa5llvh/image/upload/q_auto/in-memory-ef.jpg"
slug = "set-up-entity-framework-core-in-memory-store"
tags = ["Entity Framework Core", "C#", "DotNet", "DotNet Core"]
title = "Set up Entity Framework Core In Memory Database Provider"

+++


One of the useful features of EF Core is the In-Memory Database Provider, which is perfect for testing purposes. In this guide, we will walk through setting up EF Core with an in-memory database, demonstrating how to configure and use it effectively.

## Step-by-Step Guide

Start by creating a new .NET console using the .NET CLI and add the required packages.

```bash
dotnet new console -n EfCoreInMemoryDemo 
cd EfCoreInMemoryDemo

dotnet add package Microsoft.EntityFrameworkCore 
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

Create a simple model class.

```csharp
public class Product 
{ 
	public int Id { get; set; } 
    public string Name { get; set; } 
    public decimal Price { get; set; } 
}
```

Define a **DbContext** class to manage your entities.

```csharp
public class AppDbContext : DbContext 
{ 
	public DbSet<Product> Products { get; set; } 
    
    protected override void OnConfiguring(
    	DbContextOptionsBuilder optionsBuilder) 
	{ 
    	optionsBuilder.UseInMemoryDatabase("TestDb"); 
	} 
} 
```

In your `Program.cs`, use the **DbContext** to perform database operations.

```csharp
using var context = new AppDbContext();

// Add a new product 
context.Products.Add(new Product 
{
	Name = "Example Product", 
	Price = 9.99m 
}); 

context.SaveChanges(); 

// Query the product 
var product = context.Products.First(); 

var msg = $"Product retrieved: {product.Name} - ${product.Price}";
Console.WriteLine(msg);
```

## Conclusion

Setting up EF Core with an in-memory database is a straightforward process that can significantly enhance your testing capabilities by providing a quick and easy way to simulate database operations without the overhead of a full database setup. This setup is ideal for unit tests, allowing you to test your data access code in isolation.

With these steps, you can now efficiently configure and use EF Core's in-memory database for your .NET projects.

