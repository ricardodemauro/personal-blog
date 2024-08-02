+++
categories = ["https", "powershell", "tip", "en", "SSL", "cert"]
date = 2020-09-16T22:57:25Z
description = ""
draft = false
image = "https://res-5.cloudinary.com/dgoa5llvh/image/upload/q_auto/self-signed-cover.jpg"
slug = "generating-a-self-signed-certificate-using-powershell"
tags = ["https", "powershell", "tip", "en", "SSL", "cert"]
title = "Generating a Self-Signed Certificate using Powershell"

+++


A self-signed certificate it's very easy to create and helps on with local development and testing.

With a **Single Line** of PowerShell code we create a certificate.

First, open the `PowerShell` as **Administrator**  and run the following command:

```powershell
New-SelfSignedCertificate `
   –DnsName <DNS-Name> `
   -CertStoreLocation "cert:\LocalMachine\My"
```

The _default_ expiration is **1 year**. If you want a custom expiration date use option `-NotAfter`.

```powershell
New-SelfSignedCertificate `
   –DnsName <DNS-Name> `
   -CertStoreLocation "cert:\LocalMachine\My" `
   -NotAfter [System.DateTime]::AddYears(3)
```

{{< figure src="https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/generating-cert.png" caption="Generating Certificate" >}}

That is it. Done!! The certificate was created and stored in our `Certificate Store` of Windows.

> Note the parameter "CertStoreLocation", this is where the cert will be stored. cert:\LocalMachine means Local Machine Cert store.

Now let's export it as a `.pfx`file into a local directory.

In the same PowerShell window run the following commands.

```powershell
#create a password for our cert
$pwd = ConvertTo-SecureString -String "SOME-PASSWORD" -Force -AsPlainText

#finds the certificate in our local store
$cert = Get-ChildItem -Path cert:\LocalMachine\my | where Subject -eq "CN=rmauro.dev" 

#exports the certificate to temp directory
Export-PfxCertificate -FilePath c:\temp\rmauro.dev.pfx -Password $pwd -Cert $cert
```

> In my scenario, the cert name is rmauro.dev. Change it to yours.

Check the directory _temp_ to find the certificate - `rmauro.dev.pfx`.

{{< figure src="https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cert-powershell.png" >}}

Leave a comment / Subscribe!

