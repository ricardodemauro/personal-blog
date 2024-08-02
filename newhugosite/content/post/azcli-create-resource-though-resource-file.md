+++
categories = ["Azure", "az-cli", "resource-group", "Automation", "en"]
date = 2019-06-02T18:49:19Z
description = ""
draft = false
image = "https://res-3.cloudinary.com/dgoa5llvh/image/upload/q_auto/az-cli-cover.png"
slug = "azcli-create-resource-though-resource-file"
tags = ["Azure", "az-cli", "resource-group", "Automation", "en"]
title = "az cli (bash) create resource though Resource File"

+++


The resource file `azuredeploy.json`

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS",
            "Premium_LRS"
          ],
          "metadata": {
            "description": "Storage Account type"
          }
        },
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        }
      },
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "apiVersion": "2018-07-01",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        },
        "storageUri": {
            "type":"string",
            "value":"[reference(variables('storageAccountName')).primaryEndpoints.blob]"
        }
      }
    }
    

Go to `https://shell.azure.com` and open your subscription.

In **bash** format (not *PowerShell*) run the following scripts.

First - Create the Resource Group if doesn't exist
    
    az group create --name ExampleGroup --location "Central US"

Second - Upload the json file into the storage account of shell

![Shell upload](__GHOST_URL__/content/images/2019/06/shell-upload.PNG)
Select the template file to be uploaded

Finally - Deploy the resources into the *Resource Group* created

    az group deployment create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS


###Parameters file
The parameters file can be used to pass parameters instead of command line.

Name: `azuredeploy.parameters.json`

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "storageAccountType": {
             "value": "Standard_GRS"
         }
      }
    }

The command change a little.

    az group deployment create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters azuredeploy.parameters.json

###Sample Azure Storage template

https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json

