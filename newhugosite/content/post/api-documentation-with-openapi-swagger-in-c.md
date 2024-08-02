+++
date = 2023-04-18T23:14:35Z
description = ""
draft = true
image = "https://res-3.cloudinary.com/dgoa5llvh/image/upload/q_auto/open-api-cover.webp"
slug = "api-documentation-with-openapi-swagger-in-c"
title = "Documenting C# APIs with Open API and Swagger"

+++


API documentation is an essential aspect of software development. And the Open API documentation is one of the widely used standards for documenting RESTful APIs.

Swagger is an open-source tool that simplifies the process of creating and documenting RESTful APIs that conform to the Open API specification. In this article, we will discuss how to document your API using Swagger in C#.

In this issue let's use **Swashbuckle** packages to create the Open API documentation.

## Adding to Our API

The following steps will guide you through the process of documenting your API using Swagger in C#.

In this sample let's build a Movies API with Create, Update, Delete and Read operations.

### Create a New C# Project

Let's create our Web API Application using the `ASP.NET Core Empty` template with .NET 6 and name it **Labs.MoviesAPI**.

{{< figure src="https://res-5.cloudinary.com/dgoa5llvh/image/upload/q_auto/open-api-empty-project.png" >}}

## Install the Swagger NuGet Package

Install the package dependencies for our project.

```bash
dotnet add Microsoft.OpenApi
dotnet add Swashbuckle.AspNetCore.Swagger
dotnet add Swashbuckle.AspNetCore.SwaggerGen
dotnet add Swashbuckle.AspNetCore.SwaggerUI
```

## Build the Movies API

Let's create the Movies API using the following code:

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/", () => "Hello World!");

//returns the list of movies
app.MapGet("/movies", () => MovieDb.Movies);

//returns a movie based on Id
app.MapGet("/movies/{id:int}", (int id) =>
{
    var movie = MovieDb.Movies.FirstOrDefault(m => m.Id == id);
    return movie is not null ? Results.Ok(movie) : Results.NotFound();
});

//creates a new movie entry
app.MapPost("/movies/{id}", (int id, Movie movie) =>
{
    MovieDb.Movies.Add(movie);
    return Results.Created($"/movies/{id}", movie);
});

app.Run();


public record class Movie(int Id, string Name);

//fake movie database
public static class MovieDb
{
    public static readonly IList<Movie> Movies = new[] 
    { 
    	new Movie(1, "Movie 1"),
        new Movie(2, "Movie 2")
    };
}
```

Now our **Movie API** has 3 operation to manage the "Fake Database".

## Set Up Swagger in the Project

In order to use `Swashbuckle` API to auto generate our Swagger specification we should add the dependencies to our container and configure it in the AspNet Pipeline.

```csharp
var builder = WebApplication.CreateBuilder(args);

//enabled inspection of all Enpoints
builder.Services.AddEndpointsApiExplorer();

//dependency of Swashbuckle to generate the swagger file
builder.Services.AddSwaggerGen();

var app = builder.Build();

app.UseSwagger();

// remaining code
```

With the existing code we're able to see the `swagger.json` file already.

Let's go ahead and run the application and load the page [`https://localhost:{PORT}/swagger/v1/swagger.json`](https://localhost:7261/swagger/v1/swagger.json)

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "Labs.MoviesAPI",
    "version": "1.0"
  },
  "paths": {
    "/": {
      "get": {
        "tags": [
          "Labs.MoviesAPI"
        ],
        "responses": {
          "200": {
            "description": "Success",
            "content": {
              "text/plain": {
                "schema": {
                  "type": "string"
                }
              }
            }
          }
        }
      }
    }
    //more code goes down
  }
}
```

## Step 4: Configuring Swagger Generation

Open the swagger.json file and configure it according to your API. You can add a description, version number, and other relevant information about your API.

CODE GOES HERE

Step 5: Generate Swagger UITo generate Swagger UI, we'll need to install the Swashbuckle NuGet package. In the Package Manager Console, run the following command:

CODE GOES HERE

This command installs the necessary dependencies for Swagger UI to work.

Step 6: Add Swagger UI to Your ProjectRight-click on your project in the Solution Explorer and select Add > New Item. Select HTML Page and give it a name, such as swagger.html.

CODE GOES HERE

This will create a new HTML file in your project.

Step 7: Configure Swagger UIOpen the swagger.html file and configure it to load your swagger.json file. You can also customize the UI to match your project's branding.

CODE GOES HERE

Step 8: Run Your ProjectNow that everything is set up, run your project and navigate to the Swagger UI page ([http://localhost](http://localhost):/swagger.html). You should see a user-friendly interface that allows you to test and interact with your API.

ConclusionSwagger is an excellent tool for documenting RESTful APIs that conform to the OpenAPI specification. It simplifies the process of creating and documenting APIs, making it easier for developers to understand and use them. Swagger also provides a user-friendly interface for testing and interacting with APIs. By following the steps outlined in this article, you can start documenting your API using Swagger in C# today.

