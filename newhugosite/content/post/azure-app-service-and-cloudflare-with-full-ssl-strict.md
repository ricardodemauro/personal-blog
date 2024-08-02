+++
categories = ["Azure", "devops", "en", "security", "websecurity", "https"]
date = 2019-12-15T23:39:13Z
description = ""
draft = false
image = "https://res-3.cloudinary.com/dgoa5llvh/image/upload/q_auto/azure-cloudflare-cover.png"
slug = "azure-app-service-and-cloudflare-with-full-ssl-strict"
tags = ["Azure", "devops", "en", "security", "websecurity", "https"]
title = "Azure App Service and Cloudflare with Full SSL (Strict)"

+++


In this article we will set up Cloudflare as a reverse proxy and Azure Web Apps as a web service. Looking for the best security configuration that Cloudflare offers in the free tier.

At the end we will have the following configuration:

- CloudFlare as reverse proxy
- Azure Web App as a web service
- Valid SSL (green lock)
- Full trust SSL between Cloudflare and Azure Web Apps (Cloudflare validating server side certificate)
- Application Domains */* (A Name) and *__GHOST_URL__/* (C Name)

Our goal beyond valid SSL (green lock) is end-to-end encryption.
Encryption between Cloudflare and the user and between Cloudflare and Azure Web App.

![illustration-strict-ssl-2](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/illustration-strict-ssl--2-.png)

## Prerequisites

- **Cloudflare Account**
- **Azure Web Apps** with application running (may be native *blank* of App Service)
- **App Service Plan** with minimum plan *Basic*
- [OpenSSL](https://www.openssl.org/) installed
- Domain configured in Cloudflare

Note: *shared* and *free* layers of *Azure App Service Plan* do not allow you to perform SSL configuration.

## Knowing the Environment

![cf-blog-1-1](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-1-1.png)

*Blog [Ghost](https://github.com/YannickRe/Ghost-Azure) running on the App Service and domain https: //ghost-azure5ae4.azurewebsites.net*

![cf-blog-2](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-2.png)
*Web App Service Application*

![cf-blog-3](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-3.png)
*Cloudflare created*

## Setting Up the Cloudflare Domain

First go to the *Custom domains* tab in Azure Web App and copy the *web app* IP.

![cf-blog-7](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-7.png)

Go to *Cloudflare* and the *DNS* tab and configure as below.

- First Record
    - Type **A**
    - Name **@**
    - Target **Web App IP**
    - Proxy status: DNS only
- Second Record
    - Type **C**
    - Name **www**
    - Target **@**
    - Proxy status: DNS only

![cf-blog-23](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-23.png)

Finally configure the TXT register.

![cf-blog-8](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-8.png)
*The TXT record shows Azure that you own the domain you want to configure.*

> After all set up we will delete this record.

Ready. We should have something like:
![cf-blog-9](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-9.png)

## Setting Up the Azure Web App Domain

We are ready to configure the Azure App Service domain.

1. Open the blade **Custom domains**
2. Click **Add custom domain**
3. Enter our domain in **Custom domain**
4. Click on **Validate**
5. Validate the domain

![cf-blog-10](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-10.png)

We must perform the steps the main domain `@` and the sub domain `www`.

Note: For the main domain (A Name - rmauro.com.br) use the record type `A record`.
For the second domain (subdomain) (C Name - www.rmauro.com.br) use the record type `C record`

After validated click **Add custom domain**

![cf-blog-11](https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-11.png)

We should have something like:

![cf-blog-12-1](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-12-1.png)

When we try to access the site we should receive the red lock. This is because the digital certificate returned is from *.azurewebsites.net. and not ours - for now.

![cf-blog-13](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-13.png)

### Setting Up Certificates

In cloudflare we will use the **Full (strict)** digital certificate template. This makes **Cloudflare** validate the certificate when communicating with the server, in this case Azure Web App.

One more layer of verification, making our application even more secure.

### Configuring encryption mode and generating keys

Open **Cloudflare** on the **SSL/TLS** tab and the **Overview** subtab select the **Full (strict)** type.

![cf-blog-14](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-14.png)

Now we will create the trusted digital certificate through Cloudflare and set it up in Azure.

Go to the **Origin Server** tab and click **Create certificate**

![cf-blog-15-1](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-15-1.png)

Leave the default settings.
- Let Cloudflare generate a private key and CSR
- Private Key RSA
- List of hostnames - as configured in the previous steps.

![cf-blog-16](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-16.png)

Copy the text content to *notepad* and save as:
- Origin Certificate -> rmauro.com.br.pem
- Private Key -> rmauro.com.br.key

![cf-blog-17](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-17.png)

#### Configuring Azure Web App with Certificate

First let's generate the key in **pfx** format using **openssl**.

Open the *command prompt* and navigate to the key folder and run the following command:

```bash
> openssl pkcs12 -inkey rmauro.com.br.key -in rmauro.com.br.pem -export -out rmauro.com.br.pfx
```

This command will prompt for *passowrd*. Enter a strong password for our key.

In the Azure portal navigate to the *Custom domains* subblade again. Now let's upload the digital certificate we just created and configure it with our domain (Green Lock).

![cf-blog-18-2](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-18-2.png)

1. Click *Add Binding*
2. Select the domain *rmauro.com.br*
3. Click *Upload PFX Certificate*
4. Select the newly generated PFX (rmauro.com.br.pfx)
5. Enter the certificate password when prompted

Okay, now we have the certificate to use on our site.

![cf-blog-19-1](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-19-1.png)

Now select *Cloudflare Origin Certificate* and *TLS/SSL Type* - *SNI SSL* and click *Add Binding*.

Do the same for the *www* subdomain.

We should have something like:

![cf-blog-20-1](https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-20-1.png)

### Cloudflare Enabled as DNS and Proxy

Go back to Cloudflare and configure Proxy DNS for the domains.

![cf-blog-21-1](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-21-1.png)

All done. Green lock and end-to-end encryption using Full (strict) cryption of Cloudflare.

I hope you guys enjoy it. Share with your friends.



