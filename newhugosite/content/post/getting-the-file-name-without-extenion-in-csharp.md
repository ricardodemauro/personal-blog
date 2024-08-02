+++
categories = ["C#", "tip", "DotNet Core", "en"]
date = 2020-09-08T17:47:00Z
description = ""
draft = false
image = "https://res-3.cloudinary.com/dgoa5llvh/image/upload/q_auto/v1/ghost-blog-images/file-name-withouth-extension-cover.jpg"
slug = "getting-the-file-name-without-extenion-in-csharp"
tags = ["C#", "tip", "DotNet Core", "en"]
title = "Getting the file name without extension in C# - #TIP"

+++


These days I was asking myself how to get the file name without the extension. I confess I was tempted to use a regex to solve this problem :)

But no worries. There is a method in `System.IO.Path` specific for this situation.

```csharp
using System.IO;

///
/// Get file name without extension
///
static string GetFileName(string path)
{
    return Path.GetFileNameWithoutExtension(path);
}

///
/// Get file name without extension
///
static string GetFileName(FileInfo fileInfo)
{
    return Path.GetFileNameWithoutExtension(fileInfo.Name);
}
```





