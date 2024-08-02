+++
categories = ["AZ-203", "Azure"]
date = 2020-01-08T17:06:26Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1542119402262-dc0e1a6e2fdd?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3wxMTc3M3wwfDF8c2VhcmNofDF8fGJsdWUlMjBza3xlbnwwfHx8fDE2ODQxMjIwNzV8MA&ixlib=rb-4.0.3&q=80&w=2000"
slug = "create-azure-app-service-though-az-cli"
tags = ["AZ-203", "Azure"]
title = "Create Azure App Service though AZ Cli"

+++


```bash
# generate a unique name and store as a shell variable
webappname=mywebapp$RANDOM

# create a resource group
az group create --location westeurope --name myResourceGroup

# create an App Service plan
az appservice plan create --name $webappname --resource-group myResourceGroup --sku FREE

# create a Web App
az webapp create --name $webappname --resource-group myResourceGroup --plan $webappname

# store a repository url as a shell variable
gitrepo=https://github.com/Azure-Samples/php-docs-hello-world

# deploy code from a Git repository
az webapp deployment source config --name $webappname --resource-group myResourceGroup --repo-url $gitrepo --branch master --manual-integration

# print out the FQDN for the Web App
echo http://$webappname.azurewebsites.net
```



