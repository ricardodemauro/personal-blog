+++
categories = ["AZ-203", "Azure", "en"]
date = 2020-01-03T22:44:57Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1558494949-ef010cbdcc31?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3wxMTc3M3wwfDF8c2VhcmNofDl8fGRhdGFjZW50ZXJ8ZW58MHx8fHwxNjg0MTIyMTI0fDA&ixlib=rb-4.0.3&q=80&w=2000"
slug = "create-azure-virtual-machine-using-powershell"
tags = ["AZ-203", "Azure", "en"]
title = "Create Azure Virtual Machine Using PowerShell"

+++


## Quick Configuration

<script src="https://gist.github.com/ricardodemauro/d2db30e26cef2aff8939a6aa888835e6.js"></script>

## Getting the public Ip

```
Get-AzPublicIpAddress -ResourceGroupName "demo003" | Select "IpAddress"
```

## Full Configured Virtual Machine

<script src="https://gist.github.com/ricardodemauro/c205bfa691a6281f525bf2bb4a9dc2fe.js"></script>

## Getting the public Ip

```
Get-AzPublicIpAddress -ResourceGroupName "demo004" | Select "IpAddress"
```

## Removing deployment

```
Remove-AzResourceGroup -Name demo003
Remove-AzResourceGroup -Name demo004
```



