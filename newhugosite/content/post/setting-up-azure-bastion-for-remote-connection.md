+++
date = 2020-04-24T00:27:23Z
description = ""
draft = true
image = "https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/bastion-create-resource.png"
slug = "setting-up-azure-bastion-for-remote-connection"
title = "Setup Azure Bastion for Remote M"

+++


Hello, in this post we'll walkthough the setup of Azure Bastion.

> Azure Bastion is a fully managed PaaS service that provides secure and seamless RDP and SSH access to your virtual machines directly through the Azure Portal. Azure Bastion is provisioned directly in your Virtual Network (VNet) and supports all VMs in your Virtual Network (VNet) using SSL without any exposure through public IP addresses.

{{< bookmark url="https://azure.microsoft.com/en-us/services/azure-bastion/" title="Azure Bastion | Microsoft Azure" description="Azure Bastion provides secure and seamless RDP and SSH access to your virtual machines directly through the Azure Portal." icon="https://azurecomcdn.azureedge.net/cvt-7e63ee798fb5c57c26a10e5149d1055e6600c12efc783ef793b34b8d06646c40/images/icon/apple-touch/180x180.png" author="" publisher="Microsoft Azure" thumbnail="https://azurecomcdn.azureedge.net/cvt-7e63ee798fb5c57c26a10e5149d1055e6600c12efc783ef793b34b8d06646c40/images/shared/social/azure-icon-250x250.png" caption="" >}}

PS.: Before starting using it wildly don't forget to check pricing before starting using it.

{{< figure src="https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/azure-bastion-architecture.jpg" caption="Architecture Design" >}}

The plan here is to connect to a virtual machine inside Azure without exposing it to internet.

**Key features**

1. RDP and SSH directly in the portal
2. Remote session over SSL for SSH/RDP
3. No public IP needed on the Azure VM
4. No need for an Agent inside the Azure VM
5. Browser support for Edge and Google Chrome

The steps are pretty simple to setup Azure Bastion. Let's go!

1. Login into the https://portal.azure.com with your account
2. Create a **new resource** and select **Bastion**

{{< figure src="https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/bastion-create-resource.png" >}}

3. Choose create or selecting a existing Resource Group

4. After you select the "region" the "virtual network" will be updated and you can select the virtual network where is your virtual machine.

{{< figure src="https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/bastion-create-resource-1.png" >}}

Select the

