+++
date = 2020-09-16T23:33:23Z
description = ""
draft = true
slug = "enabling-hsts-preload-list"
title = "Enabling HSTS preload list"

+++


## Overview

If you don't know what is HSTS I invite you to read my blog post about the conecpt of HSTS and how to enable it in your IIS website [POST](__GHOST_URL__/hsts-definition-and-how-to-enable-it-in-iis/).

> HSTS is a web security technology that secures HTTPS web servers against downgrade attacks. Downgrade attacks (also known as SSL stripping attacks) are a form of man-in-the-middle attack in which an attacker redirects web browsers from a correctly configured HTTPS web server to a malicious server.

{{< bookmark url="http://support.cloudflare.com/hc/en-us/articles/204183088" title="Understanding HSTS (HTTP Strict Transport Security)" description="Cloudflare supports HTTP Strict Transport Security (HSTS)\u00a0to help secure your HTTPS web server from man-in-the middle downgrade attacks, such as SSL stripping attacks. Overview\nPrerequisites\nEnab..." icon="https://theme.zdassets.com/theme_assets/184946/2dec4705e9ab399efdc6eef36e079aa31d1df8d9.ico" author="Updated March 30, 2020 16:02" publisher="Cloudflare Help Center" thumbnail="https://theme.zdassets.com/theme_assets/184946/714cba5e835ceb090dd94c29eb1d177c002a93b6.png" caption="" >}}

If you already enable HSTS in your website this is very good! But you can do more!

You can tell the user's browser that your website has HSTS and HTTPS enable before he even do the first connection with you.

Let's see how:

1- Go to the website [https://hstspreload.org/](https://hstspreload.org/) (it's a ugly website, but it's real).

> This website is maintained by Chromium ([https://www.chromium.org/hsts](https://www.chromium.org/hsts))

{{< bookmark url="https://www.chromium.org/hsts" title="HTTP Strict Transport Security - The Chromium Projects" description="Home of the Chromium Open Source Project" icon="https://www.chromium.org/_/rsrc/1484148948583/apple-touch-icon.png" author="" publisher="The Chromium Projects" thumbnail="https://www.chromium.org/_/rsrc/1438879449147/config/customLogo.gif?revision=3" caption="" >}}

2 - Enter your domain and that is it.

{{< figure src="https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/hsts-preload-2.png" >}}

> My domain is currently preloaded. If you have green light it's good.

