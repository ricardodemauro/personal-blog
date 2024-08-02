+++
categories = ["Azure", "AZ-203", "powershell", "azure-powershell", "Automation", "en"]
date = 2019-05-27T22:56:17Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1517694712202-14dd9538aa97?ixlib=rb-1.2.1&q=80&fm=jpg&crop=entropy&cs=tinysrgb&w=2000&fit=max&ixid=eyJhcHBfaWQiOjExNzczfQ"
slug = "create-azure-vm-though-powershell"
summary = "A right to the point guide of how to create Virtual Machines in Azure with Powershell"
tags = ["Azure", "AZ-203", "powershell", "azure-powershell", "Automation", "en"]
title = "Create Azure VM though PowerShell"

+++


Go to https://shell.azure.com and enter the following commands.


######Azure available locations.
    Get-AzureRmLocation | Format-Table

######Azure Resource Groups available in the subscription.

    Get-AzureRmResourceGroup | select ResourceGroupName

######Setting up the local variables.
    $VMLocalAdminUser = "myAdminUser"
    $VMLocalAdminSecurePassword = ConvertTo-SecureString "MyStrongPassword@123456789" -AsPlainText -Force
    $LocationName = "centralus"
    $ResourceGroupName = "demo001PS"
    $ComputerName = "MyVM"
    $VMName = "devUScWeb002"
    $VMSize = "Standard_DS3"
    $NICName = "devUScWeb002-ip"

######Creating the Resource Group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $LocationName

######Creating the Virtual Network
    $virtualNetwork = New-AzVirtualNetwork `
      -ResourceGroupName $ResourceGroupName `
      -Location $LocationName `
      -Name $VMName `
      -AddressPrefix 10.0.0.0/16

    $subnetConfig = Add-AzVirtualNetworkSubnetConfig `
      -Name default `
      -AddressPrefix 10.0.0.0/24 `
      -VirtualNetwork $virtualNetwork

    $virtualNetwork | Set-AzVirtualNetwork

    $nic = New-AzureRmNetworkInterface `
     -Name "NIC1" `
     -ResourceGroupName $ResourceGroupName `
     -Location $LocationName `
     -SubnetId $virtualNetwork.Subnets[0].Id `
     -IpConfigurationName "IPConfig1" `
     -DnsServer "8.8.8.8", "8.8.4.4"

######Creating the Virtual Machine

    $Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword)

    $VirtualMachine = New-AzureRmVMConfig `
     -VMName $VMName -VMSize $VMSize

    $VirtualMachine = Set-AzureRmVMOperatingSystem `
     -VM $VirtualMachine `
     -Windows `
     -ComputerName $ComputerName `
     -Credential $Credential `
     -ProvisionVMAgent -EnableAutoUpdate

    $VirtualMachine = Add-AzureRmVMNetworkInterface `
     -VM $VirtualMachine `
     -Id $NIC.Id

    $VirtualMachine = Set-AzureRmVMSourceImage `
     -VM $VirtualMachine `
     -PublisherName 'MicrosoftWindowsServer' `
     -Offer 'WindowsServer' `
     -Skus '2012-R2-Datacenter' `
     -Version latest

    $job = New-AzureRmVM `
     -ResourceGroupName $ResourceGroupName `
     -Location $LocationName `
     -VM $VirtualMachine `
     -AsJob

The -AsJob option creates the VM in the background. You can continue to the next step.



######Based on:   
>https://docs.microsoft.com/en-us/powershell/module/AzureRm.Compute/New-AzureRmVM?view=azurermps-6.13.0
>
>https://docs.microsoft.com/en-us/azure/virtual-network/quick-create-powershell
>
>https://docs.microsoft.com/en-us/powershell/module/azurerm.network/new-azurermnetworkinterface?view=azurermps-6.13.0

