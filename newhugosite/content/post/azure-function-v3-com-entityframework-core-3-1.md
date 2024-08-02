+++
categories = ["C#", "pt", "Azure", "AZ-203"]
date = 2019-12-31T16:47:03Z
description = ""
draft = true
image = "https://images.unsplash.com/photo-1482005253821-5d6a2c685879?ixlib=rb-1.2.1&q=80&fm=jpg&crop=entropy&cs=tinysrgb&w=2000&fit=max&ixid=eyJhcHBfaWQiOjExNzczfQ"
slug = "azure-function-v3-com-entityframework-core-3-1"
tags = ["C#", "pt", "Azure", "AZ-203"]
title = "Azure Function V3 com EntityFramework Core 3.1"

+++


Olá pessoal, neste artigo vamos ver como criar um Azure Function V3 no Visual Studio 2019 e utilizar o Entity Framework Core 3.1 para acesso a dados.

Ao final vamos ter a seguinte configuração:

- Azure Function V3
- Entity Framework Core
- Entidade Aluno


Pré Requisitos

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- [Azure Functions Core Tools V3](https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local#v2)
- Conta Azure (opcional) para publicar

Código fonte disponível no [Github](https://github.com/ricardodemauro/az-function-ef-3).

## Criando novo projeto

Primeiro crie um novo projeto a partir do template Azure Function.

![az-01](https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/az-01.png)

Vamos chamar nosso projeto de **FuncStudents**.

![az-02](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/az-02.png)

Agora configure conforme abaixo e depois clique em **Create**

- Azure Function v3 (.Net Core)
- Http Trigger
- Storage Account: None
- Authorization Level: Anonymous

![az-03](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/az-03.png)

## Configurando o Entity Framework Core

Antes de adicionar os pacotes nuget vamos criar nosso modelo `Student`.

### Adicionando o modelo

Crie uma pasta com nome `Models` e criar a classe `Student.cs` conforme abaixo.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace FuncStudents.Models
{
    public class Student
    {
        public int Id { get; set; }

        public string Name { get; set; }

        public int Age { get; set; }

        public string CourseName { get; set; }
    }
}
```

### Configurando Entity Framework Core

Agora vamos adicionar os pacotes:
- Microsoft.EntityFrameworkCore - version 3.1.0
- Microsoft.EntityFrameworkCore.SqlServer - version 3.1.0

Upgrade do pacote:
- Microsoft.NET.Sdk.Functions de version 3.0.1 para 3.0.2

E por último realizar o upgrade to `target framework` para `netcoreapp3.1`.

Arquivo `FuncStudents.csproj`
```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
    <AzureFunctionsVersion>v3</AzureFunctionsVersion>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.EntityFrameworkCore" Version="3.1.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="3.1.0" />
    <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="3.0.2" />
  </ItemGroup>
  <ItemGroup>
    <None Update="host.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
    <None Update="local.settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
      <CopyToPublishDirectory>Never</CopyToPublishDirectory>
    </None>
  </ItemGroup>
</Project>
```

### Configurando o DataContext

Agora que temos os pacotes instalados é hora de configurar o nosso `DataContext`.

Crie uma pasta chamada **Data** na raiz do projeto e criar o arquivo `ApplicationDbContext.cs` conforme abaixo.

```csharp
using FuncStudents.Models;
using Microsoft.EntityFrameworkCore;

namespace FuncStudents.Data
{
    public class ApplicationDbContext : DbContext
    {
        public DbSet<Student> Students { get; set; }

        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> dbContextOptions)
            : base(dbContextOptions)
        {

        }
    }
}
```

### Configurando o Container de Injeção de Dependência

Agora que configuramos o `DbContext` com um `DbSet` **Students**, é hora de configurarmos o container de injeção de dependência.

Antes de criarmos nosso arquivo de **Startup** vamos adicionar o seguinte pacote `Microsoft.Azure.Functions.Extensions`.

```xml
<PackageReference Include="Microsoft.Azure.Functions.Extensions" Version="1.0.0" />
```

Depois de instalado o pacote. Crie o arquivo `Startup.cs` com o conteúdo a seguir.

## Referências

- [Azure Functions Docs](https://docs.microsoft.com/pt-br/azure/azure-functions/)



