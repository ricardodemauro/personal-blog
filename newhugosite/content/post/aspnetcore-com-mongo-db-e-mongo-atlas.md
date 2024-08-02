+++
categories = ["DotNet Core", "MongoDb", "perceptron", "C#", "pt"]
date = 2019-10-19T21:39:28Z
description = ""
draft = true
image = "https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/csharp-mongodb.png"
slug = "aspnetcore-com-mongo-db-e-mongo-atlas"
tags = ["DotNet Core", "MongoDb", "perceptron", "C#", "pt"]
title = "Asp.Net Core com MongoDb e Mongo Atlas - C#"

+++


Este tutorial cria uma API Web que executa as operações CRUD (criar, ler, atualizar e excluir) em um banco de dados NoSQL do [MongoDB](https://www.mongodb.com/what-is-mongodb).

Baseado no artigo [Microsoft Docs](https://docs.microsoft.com/pt-br/aspnet/core/tutorials/first-mongo-app?view=aspnetcore-3.0), criado por [Pratik Khandelwal](https://twitter.com/K2Prk) e [Scott Addie](https://twitter.com/Scott_Addie)
Modificado por [Ricardo Mauro](https://twitter.com/RicardoDeMauro)
Tempo de leitura: 18 minutos

Neste tutorial, você aprenderá como:

> * Configurar o Mongo Atlas e MongoDb
> * Definir usuário e senha de acesso no Mongo Atlas
> * Executar operações CRUD do MongoDB a partir de uma API Web Asp.Net Core 3

## Pré-requisitos

* [SDK do .NET Core 3.0 ou posterior](https://www.microsoft.com/net/download/all)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [C# Extensions para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=jchannon.csharpextensions)

---

##<a name="registering-mongo-atlas"></a>Configurando o MongoDB na nuvem Mongo Atlas

Acessar o [Mongo Atlas](https://www.mongodb.com/cloud/atlas) --https://www.mongodb.com/cloud/atlas.

Clique em `Try free` caso você não possua conta, ou `Sign In` se você já possui uma conta no Mongo Atlas.

####<a name="creating-account"></a> Criando uma conta para acesso ao Mongo Atlas

Preencha os dados básicos (email, first name, last name e password).

E ative a conta pelo e-mail recebido (Welcome to MongoDB) clicando em `Sign in`.

####<a name="sign-in"></a> Sign In

Entre com seu usuário e senha.

###<a name="preparing-environmnet-mongo-atlas"></a>Preparando o ambiente

O cluster é onde será armazenado os banco de dados Mongo Db (podemos criar mais de um). No nosso caso iremos utilizar o banco criado por padrão quando criamos o primeiro cluster.

1. Clicar em `Build a Cluster`.

2. Selecionar o cluster free (`Starter Clusters`) e depois clicar em `Create a cluster`.

3. Em *Cloud Provider* selecionar `Azure` e deixar selecionada região pré-selecionada `Virginia (eastus2)`.

4. Para as demais opções vamos deixar os valores padrões.

5. Agora clique em `Create Cluster` e aguarde o termino da criação do cluster.

A barra superior azul indica o status da criação do cluster. Pode demorar um pouco até o termino.

####<a name="little-context"></a>Um pouco de contexto - O que vemos aqui?

Do lado esquerdo temos as opções possíveis de configuração, como *Database Access* e *Network Access*.

- `Clusters` é onde vemos dados gerais sobre o cluster e é onde iremos obter a *string de conexão*.

- `Database Access` é onde gerenciamos usuário(s) e senha(s) para acesso a(s) base(s) de dados.

- `Network Access` é onde gerenciamos regras de firewall (liberamos IP).

- Demais opções deixo para vocês explorarem depois.

###<a name="setup-env"></a>Preparando o ambiente para nossa aplicação

#### 1 - Criando o primeiro usuário

1. Clicar em `Database Access`.

2. Depois clicar em `ADD NEW USER` do lado direito da tela (botão verde).

3. Inserir os seguintes dados:
```
>     Username = alunos
>     Password = alunos
>     User privileges = read and write to any database
```
Por último clicar em `Add User`.

A criação do usuário pode tomar algum tempo.

#### 2 - Criando regra no firewall

1. Primeiro clicar em `Network Access`.

2. Depois em `ADD IP ADDRESS`.

3. Agora em `ALLOW ACCESS FROM ANYWHERE` (isso permite acesso de qualquer IP)
Observe que utilizaremos isso para facilidade de acesso, porém não é recomendado para ambientes de produção.

4. Por fim clicar em `Confirm`.

Essa mudança também pode levar algum tempo para surtir efeito.

#### 3 - Coletando string de conexão (connectionString)

1. Clicar em `Clusters` no menu lateral esquerdo.

2. Depois clicar no botão `CONNECT` na sessão `SANDBOX`.

3. No popup aberto clicar em `Connect your application`.

![connect-cluster](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/connect-cluster.png)

4. Na sessão `Driver` selecionar `C# / .NET` e em `version` selecionar `2.5 or later`.

5. Copiar a `connectionString` para o notepad e substituir o `<password>` pela senha que criamos (alunos) e substituir também `<usuario>` caso já não esteja preenchido.

Resultado deverá ser algo como:

> mongodb+srv://alunos:alunos@cluster0-XXXXX.azure.mongodb.net/test?retryWrites=true&w=majority

Ótimo! Temos o nosso banco de dados pronto para nossa aplicação. Já podemos começar a criação da API Web com ASP.NET Core.

##<a name="creating-first-web-app"></a> Criando o projeto da Web API do ASP.NET Core

1. Primeiro crie uma pasta vazia chamada **MongoWebApi** dentro da pasta **Documentos**. Este será nosso local de trabalho (ou workspace em inglês), neste pasta será depositado todos os nossos código-fonte csharp, arquivos de configuração, compilados (nossas dll's) e etc.

2. Agora abra o **Visual Code** e abra a nossa pasta de trabalho (**Arquivo** - **Abrir pasta...** ou se inglês **File** - **Open Folder...**) que acabamos de criar.

3. Abra o terminal utilizando o menu superior **Terminal** depois **Novo Terminal** ou **New Terminal** em inglês.

4. No terminal aberto digite

```bash
dotnet new webapi -o AlunosApi
```


Um novo projeto de API Web do ASP.NET Core será criado na pasta AlunosAPI dentro do Visual Code.

![status-1](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/status-1.png)

Caso o apareça uma caixa de diálogo perguntando se **Os ativos necessários para criar e depurar estão ausentes do 'AlunosAPI'. Deseja adicioná-los?** . Selecione **Sim** na barra superior.

2. Novamente no mesmo **Terminal Integrado** execute os seguintes comandos para instalar o driver .NET para MongoDB:

```bash line-numbers
cd AlunosAPI
dotnet add AlunosApi.csproj package MongoDB.Driver
```

Isso irá adicionar as DLL's do MongoDb ao nosso projeto.

---

## Removendo conteúdo desnecessário ao nosso projeto

Remova os arquivos abaixo.

- WeatherForecast.cs
- Controllers/WeatherForecastController.cs

![status-2](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/status-2.png)

## Adicionando o modelo de entidade ao nosso projeto

Primeiro adicione uma pasta chamada *Models* à raiz do projeto.
 
Botão direito na pasta **AlunosAPI** e depois **Nova pasta** ou **New folder** com nome **Models** (atenção as letras maiúsculas e minúsculas).

Depois crie uma classe com nome `Aluno` ao diretório *Models*. Botão direito na pasta **Models** depois `New C# Class`.

![status-3](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/status-3.png)

Por último substitua o conteúdo do arquivo criado pelo conteúdo abaixo.

```csharp line-numbers
using MongoDB.Bson;
using MongoDB.Bson.Serialization.Attributes;

namespace AlunosApi.Models
{
	public class Aluno
	{
		[BsonId]
		[BsonRepresentation(BsonType.ObjectId)]
		public string Id { get; set; }

		[BsonElement("Name")]
		public string Nome { get; set; }

		public string Email { get; set; }
	}
}    
```

   Na classe anterior, a propriedade `Id`:

   * É necessária para mapear o objeto CLR (Common Language Runtime) para a coleção do MongoDB.
   * É anotada com [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) para ser designada como a chave primária do documento.
   * É anotada com [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) para permitir a passagem do parâmetro como o tipo `string` em vez de uma estrutura [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm). O Mongo processa a conversão de `string` para `ObjectId`.

   A propriedade `Nome` é anotada com o atributo [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm). O valor do atributo de `Name` representa o nome da propriedade da coleção do MongoDB.

## <a name="adding-a-configuration-model"></a> Adicionando o modelo de configuração

Primeiro substitua o conteúdo do arquivo *appsettings.json* pelo abaixo:

```javascript line-numbers
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },

  "AllowedHosts": "*",

  "AlunosDatabaseSettings": {
    "AlunosCollectionName": "Alunos",
    "ConnectionString": "{VALOR COPIADO NO NOTEPAD}",
    "DatabaseName": "AlunosDb"
  }
}
```

Não esqueca de substituir o **{VALOR COPIADO NO NOTEPAD}** pelo valor real.

Agora crie o arquivo *AlunosDatabaseSettings.cs* no diretório *Models* com o código a seguir:

```csharp line-numbers
namespace AlunosApi.Models
{
	public class AlunosDatabaseSettings : IDatabaseSettings
	{
		public string AlunosCollectionName { get; set; }
		public string ConnectionString { get; set; }
		public string DatabaseName { get; set; }
	}

	public interface IDatabaseSettings
	{
		string AlunosCollectionName { get; set; }
		string ConnectionString { get; set; }
		string DatabaseName { get; set; }
	}
}
```

A classe `AlunosDatabaseSettings` é usada para armazenar os valores de propriedade `AlunosDatabaseSettings` do arquivo *appsettings.json*. Os nomes de propriedade JSON e C# são nomeados de forma idêntica para facilitar o processo de mapeamento.

A seguir adicione o código ao `Startup.ConfigureServices` (arquivo Startup.cs na raiz do projeto):

```csharp line-numbers
public void ConfigureServices(IServiceCollection services)
{
	// requires using Microsoft.Extensions.Options
	services.Configure<AlunosDatabaseSettings>(Configuration.GetSection(nameof(AlunosDatabaseSettings)));

	services.AddSingleton<IDatabaseSettings>(sp =>sp.GetRequiredService<IOptions<AlunosDatabaseSettings>>().Value);

	//... código pré-existente (não apagar)
}
```

Explicando o código anterior:

   * A instância de configuração à qual a seção `AlunosDatabaseSettings` do arquivo *appsettings.json* é associada é registrada no contêiner de DI (Injeção de Dependência). Por exemplo, a propriedade `ConnectionString` de um objeto `AlunosDatabaseSettings` é populada com a propriedade `AlunosDatabaseSettings:ConnectionString` no *appsettings.json*.
   * A interface `IDatabaseSettings` é registrada na DI com um [tempo de vida do serviço](xref:fundamentals/dependency-injection#service-lifetimes) singleton. Quando inserida, a instância da interface é resolvida para um objeto `AlunosDatabaseSettings`.

Agora adicione o seguinte trecho de código na parte superior do *Startup.cs* para resolver as referências `AlunosDatabaseSettings` e `IDatabaseSettings`:

```csharp line-numbers
using AlunosApi.Models;
using Microsoft.Extensions.Options;
```

Agora execute o comando no terminal integrado `dotnet build` para validar o código produzido até agora.

A saída do comando deve ser algo como:

```bash line-numbers
> dotnet build

Build succeeded.
    0 Warning(s)
    0 Error(s)
```

Caso possua Erro(s) reviso o código até aqui.

#### Estrutura do projeto até aqui

![ex1-3](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/ex1-3.png)

## Adicionando um serviço de operações CRUD

1. Cria o diretório *Services* abaixo da pasta **AlunosApi** (raiz do projeto).
1. Adicione uma classe chamada `AlunosService` ao diretório *Services* recem criado com o seguinte código:

```csharp line-numbers
using AlunosApi.Models;
using MongoDB.Driver;
using System.Collections.Generic;
using System.Linq;

namespace AlunosApi.Services
{
    public class AlunosService
    {
        private readonly IMongoCollection<Aluno> _alunos;

        public AlunosService(IDatabaseSettings settings)
        {
            var client = new MongoClient(settings.ConnectionString);
            var database = client.GetDatabase(settings.DatabaseName);

            _alunos = database.GetCollection<Aluno>(settings.AlunosCollectionName);
        }

        public List<Aluno> Get() => _alunos.Find(aluno => true).ToList();

        public Aluno Get(string id) => _alunos.Find<Aluno>(aluno => aluno.Id == id).FirstOrDefault();

        public Aluno Create(Aluno aluno)
        {
            _alunos.InsertOne(aluno);
            return aluno;
        }

        public void Update(string id, Aluno alunoIn) => _alunos.ReplaceOne(aluno => aluno.Id == id, alunoIn);

        public void Remove(Aluno alunoIn) => _alunos.DeleteOne(aluno => aluno.Id == alunoIn.Id);

        public void Remove(string id) => _alunos.DeleteOne(aluno => aluno.Id == id);
    }
}
```


No código anterior, uma instância `IDatabaseSettings` é recuperada da DI por meio da injeção de construtor. Essa técnica fornece acesso para os valores de configuração do *appsettings.json* que foram adicionados na seção [Adicionando o modelo de configuração](#adding-a-configuration-model).

1. Agora adicione o código a seguir a `Startup.ConfigureServices`:


```csharp line-numbers
public void ConfigureServices(IServiceCollection services)
{
	//--código pré-existente (não apagar)

	//adicionar antes de services.AddControllers();
	services.AddSingleton<AlunosService>();
	
	//--código pré-existente (não apagar)
}
```

No código anterior, a classe `AlunosService` é registrada com a DI para dar suporte à injeção de construtor nas classes consumidoras. O tempo de vida do serviço singleton é mais apropriado porque `AlunosService` usa uma dependência direta de `MongoClient`. De acordo com as [Diretrizes oficiais de reutilização do cliente Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), o `MongoClient` deve ser registrado na DI com um tempo de vida do serviço singleton.

1. Não se esqueça de adicionar o seguinte código na parte superior do *Startup.cs* para resolver a referências `AlunosService`:

```csharp line-numbers
using AlunosApi.Services;
```

Rode o novamente comando `dotnet build` no **Terminal** para verificarmos o estado do nosso código até aqui.
Saída deve ser algo como:

```bash line-numbers
> dotnet build

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:05.22
```


A classe `AlunosService` usa os seguintes membros `MongoDB.Driver` para executar operações CRUD em relação ao banco de dados:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; lê a instância do servidor para executar operações de banco de dados. O construtor dessa classe é fornecido na cadeia de conexão do MongoDB:

```csharp line-numbers
public AlunosService(IDatabaseSettings settings)
{
    var client = new MongoClient(settings.ConnectionString);
    var database = client.GetDatabase(settings.DatabaseName);

    _alunos = database.GetCollection<Aluno>(settings.AlunosCollectionName);
}
```

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; representa o banco de dados Mongo para execução de operações. Este tutorial usa o método [GetCollection\<\TDocument>(coleção)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) genérico na interface para obter acesso aos dados em uma coleção específica. Execute operações CRUD em relação à coleção depois que esse método for chamado. Na chamada de método `GetCollection<TDocument>(collection)`:

  * `collection` representa o nome da coleção.
  * `TDocument` representa o tipo de objeto CLR armazenado na coleção.

`GetCollection<TDocument>(collection)` retorna um objeto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) que representa a coleção. Neste tutorial, os seguintes métodos são invocados na coleção:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; exclui um único documento que correspondem aos critérios de pesquisa fornecidos.
* [Find<\TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; retorna todos os documentos na coleção que correspondem aos critérios de pesquisa fornecidos.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; insere o objeto fornecido como um novo documento na coleção.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; substitui o único documento que corresponde aos critérios de pesquisa fornecidos com o objeto fornecido.

## <a name="add-a-controller"></a>Adicionando um controlador (controller)

Adicione uma classe `AlunosController` ao diretório *Controllers* (caso pasta não exista crie uma na raiz do projeto) com o seguinte código:

```csharp line-numbers
using AlunosApi.Models;
using AlunosApi.Services;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace AlunosApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class AlunosController : Controller
    {
        private readonly AlunosService _alunosService;

        public AlunosController(AlunosService alunosService)
        {
            _alunosService = alunosService;
        }

        [HttpGet]
        public ActionResult<List<Aluno>> Get() => _alunosService.Get();

        [HttpGet("{id:length(24)}", Name = "GetAluno")]
        public ActionResult<Aluno> Get(string id)
        {
            var aluno = _alunosService.Get(id);

            if (aluno == null)
                return NotFound();

            return aluno;
        }

        [HttpPost]
        public ActionResult<Aluno> Create(Aluno aluno)
        {
            _alunosService.Create(aluno);

            return CreatedAtRoute("GetAluno", new { id = aluno.Id.ToString() }, aluno);
        }

        [HttpPut("{id:length(24)}")]
        public IActionResult Update(string id, Aluno alunoIn)
        {
            var aluno = _alunosService.Get(id);

            if (aluno == null)
                return NotFound();

            _alunosService.Update(id, alunoIn);

            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public IActionResult Delete(string id)
        {
            var aluno = _alunosService.Get(id);

            if (aluno == null)
                return NotFound();

            _alunosService.Remove(aluno.Id);

            return NoContent();
        }
    }
}
```

O controlador da API Web:

* Usa a classe `AlunosService` para executar operações CRUD.
* Contém métodos de ação para dar suporte a solicitações GET, POST, PUT e DELETE HTTP.
* Chama o <xref:System.Web.Http.ApiController.CreatedAtRoute*> no método de ação `Create` para retornar uma resposta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). O código de status 201 é a resposta padrão para um método HTTP POST que cria um recurso no servidor. `CreatedAtRoute` também adiciona um cabeçalho `Location` à resposta. O cabeçalho `Location` especifica o URI do livro recém-criado.

#### Estrutura do projeto até aqui

![ex2](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/ex2.png)

## <a name="test-the-web-api"></a>Testando a API Web

Primeiro **compile** e execute o nosso aplicativo. Para isso clique em `Debug` na barra superior do Visual Code e depois `Start Debugging`. 
Isso demorar um pouco na primeira executação. Você pode acompanhar o status do compilação através do terminal.

Caso seja sua primeira vez executando um aplicativo .Net no Visual Code, lhe será apresentado a seguinte janela.

![status-4-1](https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/status-4-1.png)

Selecione a opção **.NET Core**. Isso irá fazer o Visual Code gerar os arquivos de configuração necessários para depurar a aplicação. 
Execute novamente `Debug` e `Start Debugging`.

Quando pronto uma janela de navegador deverá abrir automaticamente com o endereço *https://localhost:5001*.

>Na primeira execução pode lhe ser alertado sobre um certificado digital não confiável. Você pode aceitar o risco do certificado digital. Isso ocorre devido ao certificado digital de *tempo de desenvolvimento* (não confiável em ambiente produtio) do Asp.Net Core (utilizado somente em tempo **Desenvolvimento**). Por isso podemos confiar.

Agora navegue até `https://localhost:5001/api/alunos` para testar o método de ação `GET` sem parâmetros. A seguinte resposta JSON deve ser exibida:

```json
   []
```

Perfeito! Esse retorno de array vazio é esperado pois não temos registros no Mongo Db (ainda !!!). 
Isso é ótimo, conseguimos validar os seguintes items:

* Controller Alunos
* Leitura do Arquivo de Configuração
* Acesso ao Mongo Db através do Mongo Atlas

####Executando testes mais elaborados

Para testes mais elaborados vamos utilizar a ferramenta [Insomnia](https://insomnia.rest/download/). Faça o download e instale-o caso você não o tenha.

![insomia](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/insomia.png)

Você também pode utilizar outras ferramentas, como POSTMan ou ARC. Neste caso decidi pelo Insomia.

Salve o conteúdo abaixo com o nome alunos-insomia.yaml. Este arquivo contém os testes de GET / POST / PUT e DELETE (deixei pronto para facilitar).

```javascript line-numbers
_type: export
__export_format: 4
__export_date: 2019-10-20T23:22:17.814Z
__export_source: insomnia.desktop.app:v7.0.3
resources:
  - _id: req_005b4fcbbf534bd68589fb228859c347
    authentication: {}
    body: {}
    created: 1571613715982
    description: ""
    headers: []
    isPrivate: false
    metaSortKey: -1571613582546.125
    method: DELETE
    modified: 1571613715982
    name: Delete Aluno
    parameters: []
    parentId: fld_fa1061f900604e57a83ac239a9dae413
    settingDisableRenderRequestBody: false
    settingEncodeUrl: true
    settingRebuildPath: true
    settingSendCookies: true
    settingStoreCookies: true
    url: https://localhost:5001/api/alunos/5daceb3f53f8fc3450886919
    _type: request
  - _id: fld_fa1061f900604e57a83ac239a9dae413
    created: 1571613715912
    description: ""
    environment: {}
    environmentPropertyOrder: null
    metaSortKey: -1571613617577
    modified: 1571613716019
    name: Alunos
    parentId: wrk_832b63dceba24667bbd70ddf633362fb
    _type: request_group
  - _id: wrk_832b63dceba24667bbd70ddf633362fb
    created: 1571527078826
    description: ""
    modified: 1571527078825
    name: Insomnia
    parentId: null
    _type: workspace
  - _id: req_fc5d374d60b342e0bb59434c4b810f2a
    authentication: {}
    body: {}
    created: 1571613715997
    description: ""
    headers: []
    isPrivate: false
    metaSortKey: -1571613582496.125
    method: GET
    modified: 1571613715997
    name: Get Aluno Por Id
    parameters: []
    parentId: fld_fa1061f900604e57a83ac239a9dae413
    settingDisableRenderRequestBody: false
    settingEncodeUrl: true
    settingRebuildPath: true
    settingSendCookies: true
    settingStoreCookies: true
    url: https://localhost:5001/api/alunos/5daceb3f53f8fc3450886919
    _type: request
  - _id: req_2320b4b3a6b54b01a2fa61c313e041a9
    authentication: {}
    body:
      mimeType: application/json
      text: |-
        {
            "id": "5daceb3f53f8fc3450886919",
            "nome": "Aluno 1 - Update",
            "email": "aluno1-update@faculdadeimpacta.com.br"
        }
    created: 1571613715964
    description: ""
    headers:
      - id: pair_a670675f0243435a9d3445694c7b58cb
        name: Content-Type
        value: application/json
    isPrivate: false
    metaSortKey: -1571613582446.125
    method: PUT
    modified: 1571613715964
    name: Update Aluno
    parameters: []
    parentId: fld_fa1061f900604e57a83ac239a9dae413
    settingDisableRenderRequestBody: false
    settingEncodeUrl: true
    settingRebuildPath: true
    settingSendCookies: true
    settingStoreCookies: true
    url: https://localhost:5001/api/alunos/5daceb3f53f8fc3450886919
    _type: request
  - _id: req_5dc593023227433bac88c0f00ee9851d
    authentication: {}
    body:
      mimeType: application/json
      text: |-
        {
        	"nome": "Aluno 1",
        	"email": "aluno1@faculdadeimpacta.com.br"
        }
    created: 1571613715946
    description: ""
    headers:
      - id: pair_d62363541a994b13b9566c92cd572b96
        name: Content-Type
        value: application/json
    isPrivate: false
    metaSortKey: -1571613582396.125
    method: POST
    modified: 1571613715946
    name: Create Aluno
    parameters: []
    parentId: fld_fa1061f900604e57a83ac239a9dae413
    settingDisableRenderRequestBody: false
    settingEncodeUrl: true
    settingRebuildPath: true
    settingSendCookies: true
    settingStoreCookies: true
    url: https://localhost:5001/api/alunos
    _type: request
  - _id: req_31184175d6b5455e86d2e9e0ec34e57e
    authentication: {}
    body: {}
    created: 1571613715929
    description: ""
    headers: []
    isPrivate: false
    metaSortKey: -1571613582346.125
    method: GET
    modified: 1571613715929
    name: Get Alunos
    parameters: []
    parentId: fld_fa1061f900604e57a83ac239a9dae413
    settingDisableRenderRequestBody: false
    settingEncodeUrl: true
    settingRebuildPath: true
    settingSendCookies: true
    settingStoreCookies: true
    url: https://localhost:5001/api/alunos
    _type: request
  - _id: env_7f3527ea80965a06126f1429c010d91e789910d7
    color: null
    created: 1571527079133
    data: {}
    dataPropertyOrder: null
    isPrivate: false
    metaSortKey: 1571527079133
    modified: 1571527079133
    name: Base Environment
    parentId: wrk_832b63dceba24667bbd70ddf633362fb
    _type: environment
  - _id: jar_7f3527ea80965a06126f1429c010d91e789910d7
    cookies: []
    created: 1571527079152
    modified: 1571527079152
    name: Default Jar
    parentId: wrk_832b63dceba24667bbd70ddf633362fb
    _type: cookie_jar
```
>alunos-insomia.yaml

Perfeito, agora clique em **Import from file** e importe o arquivo que acabamos de gravar.

Faça o primeiro teste, clique no item **Get Alunos**, devemos obter o mesmo array vazio.

![status-5](https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/status-5.png)

>Novamente pode lhe ser alertado sobre *SSL Validation*. Por isso também vamos aceitar o alerto clicando em *Disable SSL Validation*, depois na sessão *Network* desmarcar o item **Validate certificates**.

Agora teste as demais opções, como **Create Aluno** e depois volte ao **Get Alunos** para ver o aluno criado.

Tente utilizar o **Update Aluno** (não se esqueça de atualizar na rota (URL) com o Id do aluno).

---

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a criação de APIs Web do ASP.NET Core, confira os seguintes recursos:

* [Versão do artigo original no YouTube](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* Artigo original: [Microsoft Docs](https://docs.microsoft.com/pt-br/aspnet/core/tutorials/first-mongo-app?view=aspnetcore-3.0)
* Inclua novos modelos como "Turma", "Curso", etc.

