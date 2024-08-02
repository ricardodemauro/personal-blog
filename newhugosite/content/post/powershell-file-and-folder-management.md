+++
date = 2023-04-26T13:32:07Z
description = ""
draft = true
image = "https://images.unsplash.com/photo-1571232151946-f7f00c61ade7?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwxMTc3M3wwfDF8c2VhcmNofDF8fHNjcmlwdHxlbnwwfHx8fDE2ODI1MTYwNjE&ixlib=rb-4.0.3&q=80&w=2000"
slug = "powershell-file-and-folder-management"
title = "PowerShell File and Folder Management"

+++




```ps1
Test-Path -Path "some path"
```

{{< bookmark url="https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/new-item" title="New-Item (Microsoft.PowerShell.Management) - PowerShell" description="The New-Item cmdlet creates a new item and sets its value. The types of items that can be created depend on the location of the item. For example, in the file system, New-Item creates files and folders. In the registry, New-Item creates registry keys and entries. New-Item can also set the value of t…" icon="https://learn.microsoft.com/favicon.ico" author="sdwheeler" publisher="Microsoft Learn" thumbnail="https://learn.microsoft.com/media/logos/logo-powershell-social.png" caption="" >}}

```ps1
Function Clear-Path ($path) {
    Write-Output "[Clear-Path] init " $path

    Get-ChildItem -Path $path -Include *.* -File -Recurse | foreach { $_.Delete() }
    Get-ChildItem -Path $path -Directory | Remove-Item -Recurse

    Write-Output "[Clear-Path] end" $path
}
```



