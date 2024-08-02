+++
categories = ["Javascript", ".NET 5", "Authentication"]
date = 2021-07-15T23:15:13Z
description = ""
draft = true
image = "https://res-5.cloudinary.com/dgoa5llvh/image/upload/q_auto/v1/ghost-blog-images/pt-api-key-cover.jpg"
slug = "api-key-authentication-estendendo-a-implementacao-nativa"
tags = ["Javascript", ".NET 5", "Authentication"]
title = "API Key Authentication - Estendendo a implementação nativa - .NET 5"

+++


Olá, neste artigo iremos implementar e explorar o código para tratar com a autenticação do tipo "API Key". Isso tudo com apenas _três linhas de código_.

Aqui queremos a famosa solução **simples e**  **estúpida (simple and stupid)** e não uma implementação distorcida utilizando `MVC [Attributes]` ou um _middleware_ customizado para lidar com a autenticação.

**Observação:** Esse artigo aplica-se as versões do .NET 5, .NET Core 3.1 e .NET Core 2.1.

```csharp
services.AddAuthentication(ApiKeyAuthNDefaults.SchemaName)
    .AddApiKey(opt => //here is our handler
    {
        opt.ApiKey = "Hello-World";
        opt.QueryStringKey = "key";
    });
```

OK ok ok. Eu sei que é difícil encontrar uma boa implementação de _API Key Authentication_ na internet. Acredito também ser difícil nos encontrarmos em uma situação que precisemos utilizar de _API Key Authentication_.Mas agora você encontrou! Espero que você goste e deixe seu comentário ao fim desse artigo :)

## Introdução

O ASP.NET já possui toda a infraestrutura de código de Autenticação/Autorização e nos permite estender sua implementação afim de customizarmos com uma implementação própria. Nós podemos estender a implementação nativa para criar nossa lógica de validação (usuário + senha) para consultar um banco de dados ou de qualquer lugar.

Utilizando-se do método `AddScheme` iremos implementar e criar uma lógica para validação da _APIKey_ ao serviço de autenticação.

Tudo começa com o método `services.AddAuthentication()`, ele possui um construtor que nos permite acessar ao o método `AddScheme`. É aqui que nosso manipulador `Authentication ApiKey` será configurado.

## Vamos ao Código

Vamos começar criando o arquivo `ApiKeyAuthNOptions.cs`. Ele será responsável pelas configurações do serviço `ApiKeyAuthN.cs` (a ser criado posteriormente).Definimos as propriedades `QueryStringKey` e `ApiKey`.

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

A segunda etapa é o arquivo `ApiKeyAuthN.cs` com o seguinte conteúdo.

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

A classe `AuthenticationHandler` é responsável por realizar a validação e criar o _Ticket_ de Autenticação para o cliente.

Acho que você pode adivinhar onde colocar a lógica de validação, certo? Aqui está a implementação.

```csharp
protected override Task<AuthenticateResult> HandleAuthenticateAsync()
{
    var apiKey = ParseApiKey(); // handles parsing QueryString or Header

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

No método `ParseApiKey` lemos somente a _QueryString_, porém poderiamos também ler do _RequestHeader_.

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

Aqui deixo uma implementação básica do método `BuildPrincipal`, o resto deixo para você desenvolver. Neste método devemos personalizar o `ClaimIdentity` com os `Claims` que são necessários ao seu cliente, como _Role, PhoneNumber, Issuer, Partner Id,_ entre outros.

## Concluindo - Falta pouco agora

Já temos tudo que precisamos para iniciar com a autenticação _API Key_. Abra o arquivo `Startup.cs` e adicione o seguinte conteúdo.

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

No método `AddScheme` configuramos o _serviço_ de autenticação para utilizar o nosso **manipulador de autenticação**.

Nossa API Key aqui é **"Hello-World"**. Iremos utilizá-lo em nossos testes a seguir.

A seguir iremos configurar o método `Configure` para usar os middlewares de **autenticação** e **autorização** nativos.

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

Adicionamos o método `WriteClaims`. Com ele iremos visualizar as `Claims` do usuário.

## Executando a solução

{{< figure src="https://res-1.cloudinary.com/dgoa5llvh/image/upload/q_auto/v1/ghost-blog-images/pt-p1.png" caption="Página inicial sem Autenticação" >}}

Chamada na _Rota_ "/" **SEM Api Key**.

{{< figure src="https://res-3.cloudinary.com/dgoa5llvh/image/upload/q_auto/v1/ghost-blog-images/pt-p2.png" caption="Página inicial com Autenticação" >}}

Chamada na _Rota_ "/" **COM Api Key**.

SUCESSO!!!

## Tornando-o ainda mais fácil para o Startup

Agora vamos criar um método _builder_ de extensão para configurar nosso **manipulador** `AddApiKey`. Ai sim podemos utilizar aquelas três linhas que mencionamos no início do artigo.

Crie o arquivo `ApiKeyAuthNExtensions.cs` com o seguinte conteúdo.

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

Assim podemos utilizar o novo método de extensão `AddApiKey` ao invés do `AddScheme`.Modifique o método `Configure` na classe `Startup` e substitua pelo novo método.

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

É isso! Espero que você tenha gostado. Deixe um comentário e diga o que achou, dificuldade, sugestões, melhorias, enfim! Até a próxima.

**Não esqueça de compartilhar!**

Originalmente publicado em [https://rmauro.dev/api-key-authentication-estendendo-a-implementacao-nativa/](__GHOST_URL__/api-key-authentication-estendendo-a-implementacao-nativa/)

## Código fonte para Download

{{< bookmark url="https://github.com/ricardodemauro/article-APIAuthentication" title="ricardodemauro/article-APIAuthentication" description="Contribute to ricardodemauro/article-APIAuthentication development by creating an account on GitHub." icon="https://github.githubassets.com/favicons/favicon.svg" author="ricardodemauro" publisher="GitHub" thumbnail="https://opengraph.githubassets.com/89351ae4fcbf6aed7225bd1eeffb952592c4125f053db0f2ce3b99ebcdca11fa/ricardodemauro/article-APIAuthentication" caption="" >}}

**Observação:** Existe um _nuget package_ que trás essa implementação pronta [https://github.com/mihirdilip/aspnetcore-authentication-apikey](https://translate.google.com/website?sl=en&tl=pt&ajax=1&elem=1&se=1&u=https://github.com/mihirdilip/aspnetcore-authentication-apikey) .

