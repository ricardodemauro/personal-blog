+++
categories = ["pt", ".NET 5", "C#", "HealthCheck"]
date = 2021-07-16T02:10:38Z
description = ""
draft = true
image = "https://res-4.cloudinary.com/dgoa5llvh/image/upload/q_auto/v1/ghost-blog-images/endpoing-cover.jpg"
slug = "health-checks-em-aplicacoes-net-5"
tags = ["pt", ".NET 5", "C#", "HealthCheck"]
title = "Health Checks em Aplicações .NET 5"

+++


## Padrão de monitoramento de Endpoint de saúde

`Health Checks` em aplicações **.NET 5** é muito simples. Apenas com algumas linhas de código podemos configurar tudo para monitorar a **saúde de nosso aplicativo.**

> Implemente verificações funcionais em um aplicativo que ferramentas externas possam acessar por meio de endpoints expostos em intervalos regulares. Isso pode ajudar a verificar se os aplicativos e serviços estão funcionando corretamente.

{{< figure src="https://res-3.cloudinary.com/dgoa5llvh/image/upload/q_auto/v1/ghost-blog-images/endpoint-monitoring-pattern.png" >}}

[https://docs.microsoft.com/en-us/azure/architecture/patterns/health-endpoint-monitoring](https://docs.microsoft.com/en-us/azure/architecture/patterns/health-endpoint-monitoring)

## Introdução

Nesta série de artigos iremos explorar mais sobre **Endpoint Monitoring** para aplicações web.

1. **Health Checks em Aplicações .NET 5** [esta postagem]
2. Adicionando verificação de integridade da interface do usuário
3. Monitoramento de endpoint com Azure Application Insights
4. Usando o Monitoramento de Endpoint dos Serviços de Aplicativo do Azure

Nesse artigo adicionaremos um ponto de extremidade (endpoint) para monitorar o **status de integridade** de uma aplicação .NET 5.

Vamos entender como isso é muito simples em aplicativos .NET Core / .NET 5.Com apenas algumas linhas de código, toda a infraestrutura está pronta para exibir o `_HealthStatus_` de nossos aplicativos ASP.NET.

Nesse _endpoint_ verificar a integridade de serviços como:

* Serviços de banco de dados como: SQL Server, Oracle, MySql, MongoDB, entre outros;
* Conectividade de API externa - URLs externos;
* Conectividade de disco (leitura/gravação);
* Serviços de cache, como _Redis_, _Memcache_, entre outros;
* Implementações personalizadas (algo exclusivo para seu sistema).

Existe um validador para tudo que você precisa. Porém, caso não encontre uma implementação adequada podemos criar um validador customizado.

## Adicionando um _Health Check_ básico ao ASP.NET

Primeiro, modifique o método `ConfigureServices` como descrito abaixo. Isso irá adicionar o serviço de `HealthChecks` ao container de injeção de dependência.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    //adding health check services to container
    services.AddHealthChecks();
}

```

Ainda no arquivo `Startup.cs`. No método `app.UseEndpoints()` do método `Configure`, adicione a entrada `MaphealthChecks()` para expor o _endpoint_ de monitoração.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
        if (env.IsDevelopment())
            app.UseDeveloperExceptionPage();

        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapGet("/", async context =>
            {
                await context.Response.WriteAsync("Hello World!");
            });

            endpoints.MapDefaultControllerRoute();

            endpoints.MapHealthChecks("/healthcheck");
        });
}

```

Compile o projeto, execute e acesse a url `http://{YOUR-URL}/healthcheck`. Devemos ver algo como a imagem abaixo.

{{< figure src="https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/basic-health-check.png" caption="HealthCheck Healthy" >}}

Tudo pronto para adicionarmos serviços à lista de monitoramento. Vamos adicionar o serviço **Mongo Db** à lista serviços monitorados.

## Adicionando Mongo Db a lista de serviços monitorados

Vamos escolher o item adequado da lista _do xabaril_. _O_ repositório Github do _xabaril_ existem diversa implementações de `AspNetCore.Diagnostics.HealthChecks` prontos para uso, inclusive o **Mongo Db**.

* AspNetCore.HealthChecks.System
* AspNetCore.HealthChecks.Network
* AspNetCore.HealthChecks.SqlServer
* AspNetCore.HealthChecks.MongoDb
* AspNetCore.HealthChecks.Npgsql
* AspNetCore.HealthChecks.Elasticsearch
* AspNetCore.HealthChecks.Redis
* AspNetCore.HealthChecks.MySql

> [https://github.com/xabaril/AspNetCore.Diagnostics.HealthChecks](https://github.com/xabaril/AspNetCore.Diagnostics.HealthChecks)

Adicione o pacote Nuget `AspNetCore.HealthChecks.MongoDb` ao seu projeto e modifique o método `AddHealthChecks()` para incluir o **Mongo Db**  _Health Check_.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    //adding health check services to container
    services.AddHealthChecks()
        .AddMongoDb(
        mongodbConnectionString: "YOUR-CONNECTION-STRING",
        name: "mongo", 
        failureStatus: HealthStatus.Unhealthy); //adding MongoDb Health Check
}

```

Depois disso, estamos prontos para começar a monitorar a conectividade do **Mongo Db**. Se executarmos o aplicativo novamente, veremos a seguinte saída - caso tenha configurado corretamente e tenha conectividade ao serviço **Mongo Db**.

{{< figure src="https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/basic-health-check.png" caption="HealthCheck com Mongo Db" >}}

## Adicionando um segundo _endpoint_ com mais detalhes

Vamos criar um segundo _endpoint_ com mais informações sobre o status, como qual verificação de integridade está falhando e _tags_.

```csharp
using System.Linq;
using Microsoft.Extensions.Diagnostics.HealthChecks;
using System.Text.Json;

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    /*existing code*/

    app.UseEndpoints(endpoints =>
    {
        /*existing code*/

        endpoints.MapHealthChecks("/healthcheck");

        endpoints.MapHealthChecks("/healthcheck-details",
            new HealthCheckOptions
            {
                ResponseWriter = async (context, report) =>
                {
                    var result = JsonSerializer.Serialize(
                        new
                        {
                            status = report.Status.ToString(),
                            monitors = report.Entries.Select(e => new { key = e.Key, value = Enum.GetName(typeof(HealthStatus), e.Value.Status) })
                        });
                    context.Response.ContentType = MediaTypeNames.Application.Json;
                    await context.Response.WriteAsync(result);
                }
            }
        );
    });
}

```

Execute novamente e acesse endereço `/healthcheck-details`.

{{< figure src="https://res-1.cloudinary.com/dgoa5llvh/image/upload/q_auto/v1/ghost-blog-images/second-endpoint-monitor.jpg" caption="HealthCheck Detalhes" >}}

Agora você pode personalizar a resposta do seu _endpoint_  _HealthCheck_ conforme necessidade.

No próximo artigo, mostrarei como colocar uma Iterface Gráfica sobre o _HealthCheck_.

## Código fonte para Download

[https://github.com/ricardodemauro/HerosApi-Blog](https://github.com/ricardodemauro/HerosApi-Blog)

