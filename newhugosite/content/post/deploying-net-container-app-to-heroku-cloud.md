+++
categories = ["Heroku", "Docker", "container", "devops", "en", "cloud", "DotNet Core", ".NET 5"]
date = 2019-08-18T18:15:42Z
description = ""
draft = false
image = "https://res-2.cloudinary.com/dgoa5llvh/image/upload/q_auto/heroku-cover.png"
slug = "deploying-net-container-app-to-heroku-cloud"
tags = ["Heroku", "Docker", "container", "devops", "en", "cloud", "DotNet Core", ".NET 5"]
title = "Deploying .Net container App to Heroku cloud"

+++


I'm tired of searching every time I need to deploy to heroku cloud. Being that said I'm finally writing down the steps do deploy a docker container app to Heroku cloud.

By the way, Heroku Cloud it is really cool.

###Requirements to deploy

- Docker client (windows or linux) installed [Link](https://docs.docker.com/docker-for-windows/install/)
- Heroku CLI (command line interface) installed [Link](https://devcenter.heroku.com/articles/heroku-cli)
- Application with a Dockerfile

###Changes you have to make in your Dockerfile

First remove the `Entrypoint` and `EXPOSE` statments of your Dockerfile.

    ENTRYPOINT ["dotnet", "SOME-APP.dll"]

Instead of use the `EXPOSE` command we should rely on environment variable `PORT` (Heroku sets the output port you should use) and execute a `CMD` command instead of `ENTRYPOINT` as the last command line.

    #set up as a environment variable
    CMD export ASPNETCORE_URLS=http://*:$PORT

    #or set up in the command line of execution
    CMD ASPNETCORE_URLS=http://*:$PORT dotnet ENTRYPOINT-DLL.dll

The final Dockerfile:

    FROM microsoft/dotnet:2.2-aspnetcore-runtime AS base
    WORKDIR /app

    FROM microsoft/dotnet:2.2-sdk AS build
    WORKDIR /src
    COPY . .
    RUN dotnet restore "FuraFila.WebApp/FuraFila.WebApp.csproj"
    WORKDIR "/src/FuraFila.WebApp"
    RUN dotnet build "FuraFila.WebApp.csproj" -c Release -o /app
    RUN dotnet ef database update

    FROM build AS publish
    RUN dotnet publish "FuraFila.WebApp.csproj" -c Release -o /app

    FROM base AS final
    WORKDIR /app
    COPY --from=publish /app .

    CMD export ASPNETCORE_URLS=http://*:$PORT
    RUN echo 'we are running some # of cool things'
    
    CMD ASPNETCORE_URLS=http://*:$PORT dotnet FuraFila.WebApp.dll

Also a sample Dockerfile can be found in my personal [Github](https://github.com/ricardodemauro/fura_fila/blob/master/src/Dockerfile.heroku) account.

### Pushing thing to Heroku Container Registry

Open your CLI (Command Line Tool), such as bash or command prompt and execute the following steps.

    #first login into heroku CLI
    heroku login

    #build your docker image
    docker build -t rick/furafila:heroku -f Dockerfile.heroku .

    #test your docker image. This step it's not necessary
    docker run -p 5000:5000 -d -e PORT=5000 rick/furafila:heroku

    #tag your docker image
    docker tag rick/furafila:heroku registry.heroku.com/rick-furafila/web

By the way, `rick/furafila:heroku` is the name of my application. Don't forget to replace with yours.

    #if you are not logged in into Heroku Container Registry.
    heroku container:login

    #pushing to Heroku cloud
    docker push registry.heroku.com/rick-furafila/web


####Releasing it to live

Finally tell Heroku to release it to live environment.

    heroku container:release web -a rick-furafila


Now you're free :)

###Based on articles

- https://blog.devcenter.co/deploy-asp-net-core-2-0-apps-on-heroku-eea8efd918b6

- https://codeburst.io/accessing-heroku-config-variables-in-your-vue-webpack-app-145afb32dd67

