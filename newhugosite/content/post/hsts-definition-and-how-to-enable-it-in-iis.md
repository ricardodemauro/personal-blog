+++
categories = ["IIS", "HSTS", "https", "security", "websecurity", "en"]
date = 2017-08-09T18:10:00Z
description = ""
draft = false
image = "https://res-2.cloudinary.com/dgoa5llvh/image/upload/q_auto/hsts-cover.png"
slug = "hsts-definition-and-how-to-enable-it-in-iis"
tags = ["IIS", "HSTS", "https", "security", "websecurity", "en"]
title = "HSTS - What's it and how to enable it in IIS"

+++


###HSTS, what's it?

Definition from wikipedia:
> HTTP Strict Transport Security (HSTS) is a web security policy mechanism which helps to protect websites against protocol downgrade attacks and cookie hijacking. It allows web servers to declare that web browsers (or other complying user agents) should only interact with it using secure HTTPS connections,[1] and never via the insecure HTTP protocol. HSTS is an IETF standards track protocol and is specified in RFC 6797.`


An example to help clearify:

- You decide to visit / (HTTP version), you send a request to the webserver saying the you want visit this website.

- The webserver respond with HTTP STATUS 301 (Moved Permanently) directing you to /.

![http-1](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/http-1.png)

*HTTP version request*

Once you get the response o HTTPS version you also receive a header named "Strict-Transport-Security" - The famous **HSTS**.

![hsts-headers](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/hsts-headers.png)

*Legend: HTTPS request after redirect*

![request-flow](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/request-flow.png)

*Legend: Request flow with HSTS enabled*

Now the browser know's that your website is HSTS and HTTPS enabled.
Every time you decide to go to HTTP version of this site, your browser will direct you to HTTPS version of the site, without sending a request to HTTP version first.

###How enabled it on IIS

It's very simple. Here is a how:

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="HTTP to HTTPS redirect" stopProcessing="true">
                        <match url="(.*)" />
                        <conditions>
                            <add input="{HTTPS}" pattern="off" ignoreCase="true" />
                        </conditions>
                        <action type="Redirect" 
                                url="https://{HTTP_HOST}/{R:1}"
                                redirectType="Permanent" />
                    </rule>
                </rules>
                <outboundRules>
                    <rule name="Add Strict-Transport-Security when HTTPS" enabled="true">
                        <match serverVariable="RESPONSE_Strict_Transport_Security"
                               pattern=".*" />
                        <conditions>
                            <add input="{HTTPS}" pattern="on" ignoreCase="true" />
                        </conditions>
                        <action type="Rewrite" value="max-age=31536000" />
                    </rule>
                </outboundRules>
            </rewrite>
        </system.webServer>
    </configuration>

Explaining what's going on there.

- First rule (**HTTP to HTTPS redirect**) tells IIS to redirect every request to HTTS if not there.
- Second rule (**Add Strict-Transport-Security when HTTPS**)


**Now you must be thinking with yourself, why not just add a simple HTTP-Header in config?**

The HSTS (RFC6797) spec says:
> An HTTP host declares itself an HSTS Host by issuing to UAs (User Agents) an HSTS Policy, which is represented by and conveyed via the
Strict-Transport-Security HTTP response header field over secure transport (e.g., TLS). `
 
So you should not send the header in HTTP version of the website.

####Sources:
Wikipedia
`https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security`

Hanselman's blog
`https://www.hanselman.com/blog/HowToEnableHTTPStrictTransportSecurityHSTSInIIS7.aspx`

HSTS RFC
`http://tools.ietf.org/html/rfc6797#page-12`

