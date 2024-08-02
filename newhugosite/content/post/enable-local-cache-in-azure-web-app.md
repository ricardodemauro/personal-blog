+++
categories = ["Azure", "cache", "AZ-203", "en", "DotNet"]
date = 2019-06-01T19:01:27Z
description = ""
draft = false
image = "https://res-4.cloudinary.com/dgoa5llvh/image/upload/q_auto/local-cache-cover.png"
slug = "enable-local-cache-in-azure-web-app"
tags = ["Azure", "cache", "AZ-203", "en", "DotNet"]
title = "Enable Local Cache in Azure Web App"

+++


##What is Local Cache in Azure WebApp?

Local cache in Azure Web is really a cache that caches the storage of your Web App.

##How to enable it?

To enable local cache you should go to your **Azure Subscription** them navigate to your **Web App** that you wishs to enable local cache.

Go to **"Configuration"** section then "Application Setttings" and under **"Application settings"**

You enable Local Cache on a per-web-app basis by using this app setting: `WEBSITE_LOCAL_CACHE_OPTION = Always`

![app-service-local-cache-configure-portal](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/app-service-local-cache-configure-portal.png)

***ref. https://docs.microsoft.com/pt-br/azure/app-service/overview-local-cache***

Available options:

    "WEBSITE_LOCAL_CACHE_OPTION": "Always",
    "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"

By default, the local cache size is **300 MB**.

###Warning
Maybe you don't want to enable it in your *staging* environments. In that case put the configuration only to the specific slot you're targeting.

FAQ see
https://docs.microsoft.com/pt-br/azure/app-service/overview-local-cache#frequently-asked-questions-faq

######Refs.:
>https://docs.microsoft.com/pt-br/azure/app-service/overview-local-cache

>http://involvenevolve.com/post/azure-app-services-hidden-gem-local-cache-feature/

