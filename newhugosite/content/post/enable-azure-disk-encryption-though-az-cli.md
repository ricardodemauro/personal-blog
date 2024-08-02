+++
categories = ["Azure", "Automation", "AZ-203", "az-cli", "en"]
date = 2019-05-27T23:04:34Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1477039181047-efb4357d01bd?ixlib=rb-1.2.1&q=80&fm=jpg&crop=entropy&cs=tinysrgb&w=2000&fit=max&ixid=eyJhcHBfaWQiOjExNzczfQ"
slug = "enable-azure-disk-encryption-though-az-cli"
tags = ["Azure", "Automation", "AZ-203", "az-cli", "en"]
title = "Enable Azure Disk Encryption though az-cli"

+++


######Create Azure Key Vault service
    az keyvault create \
     --name "kvDemo006" \
     --resource-group "demo001" \
     --location centralUS \
     --enabled-for-disk-encryption True

######Enable disk encryption on VM
    az vm encryption enable \
     --resource-group "kvDemo006" \
     --name "devUScWeb003" \
     --disk-encryption-keyvault "demo006" \
     --volume-type "all"

######Show disk encryption
    az vm encryption show \
     --resource-group "demo006" \
     --name "devUScWeb003"`

