+++
categories = ["DotNet Core", "en"]
date = 2017-01-04T00:40:41Z
description = ""
draft = false
slug = "how-to-migrate-asp-net-core-1-0-to-1-1"
tags = ["DotNet Core", "en"]
title = "How to migrate .Net Core 1.0 to 1.1"

+++


Hi guys!

In this post I'll show how to migrate a .Net project from 1.0 to 1.1 (current the last version) using Visual Studio.


##Preparing the solution
Let's starting by creating our starting project.

In Visual Studio go to `File -> New Project` then choose the template `ASP.NET Core Web Application (.NET Core)`

Enter the name "SampleProject" then click OK.

![1121212](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/1121212.png)

Now select `Empty` and hit OK.

![212131213](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/212131213.png)

Build the app to verify you don't have any compiler errors.

##It's time to migrate

Now that we have our project up and running let's migrate from 1.0 to 1.1.

Open the file `project.json` and update the "section" `frameworks` like the following code.

from:

    "frameworks": {
        "netcoreapp1.0": {
             "imports": [
                 "dotnet5.6",
                 "portable-net45+win8"
             ]
        }
    },


to:

    "frameworks": {
        "netcoreapp1.1": {
          "dependencies": {
            "Microsoft.NETCore.App": {
              "version": "1.1.0",
              "type": "platform"
            }
          },
          "imports": [
            "dnxcore50"
          ]
        }
      },

Now remove `Microsoft.NETCore.App` from section `dependencies` and update all nuget packages using nuget manager.


The file `project.json` should end like this.

    {
      "dependencies": {
        "Microsoft.AspNetCore.Diagnostics": "1.1.0",
        "Microsoft.AspNetCore.Server.IISIntegration": "1.1.0",
        "Microsoft.AspNetCore.Server.Kestrel": "1.1.0",
        "Microsoft.Extensions.Logging.Console": "1.1.0"
      },

      "tools": {
        "Microsoft.AspNetCore.Server.IISIntegration.Tools": "1.0.0-preview2-final"
      },

      "frameworks": {
        "netcoreapp1.1": {
          "dependencies": {
            "Microsoft.NETCore.App": {
              "version": "1.1.0",
              "type": "platform"
            }
          },
          "imports": [
            "dnxcore50"
          ]
        }
      },

      "buildOptions": {
        "emitEntryPoint": true,
        "preserveCompilationContext": true
      },

      "runtimeOptions": {
        "configProperties": {
          "System.GC.Server": true
        }
      },

      "publishOptions": {
        "include": [
          "wwwroot",
          "web.config"
        ]
      },

      "scripts": {
        "postpublish": [ "dotnet publish-iis --publish-folder %publish:OutputPath% --framework %publish:FullTargetFramework%" ]
      }
    }


Wait until all dependencies are restored and "build" again to verify you don't have any compiler errors.

Now we have our project with .Net Core 1.1.

All done guys.

