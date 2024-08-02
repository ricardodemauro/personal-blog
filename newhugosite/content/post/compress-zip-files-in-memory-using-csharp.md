+++
categories = ["C#", "tip", "snippets", "en"]
date = 2020-09-09T19:59:29Z
description = ""
draft = false
image = "https://res-4.cloudinary.com/dgoa5llvh/image/upload/q_auto/compress-cover.jpg"
slug = "compress-zip-files-in-memory-using-csharp"
tags = ["C#", "tip", "snippets", "en"]
title = "Compress files in memory (.zip) using C#"

+++


Here is a code snippet of how to compress one or many files to a zip archive in memory using C#.

> _It works in .Net Core and .Net Full Framework_

```csharp
public static byte[] GetZipArchive(params InMemoryFile[] files)
{
    byte[] archiveFile;
    using (var archiveStream = new MemoryStream())
    {
        using (var archive = new ZipArchive(archiveStream, ZipArchiveMode.Create, true))
        {
            foreach (var file in files)
            {
                var zipArchiveEntry = archive.CreateEntry(file.FileName, CompressionLevel.Fastest);

                using var zipStream = zipArchiveEntry.Open();
                zipStream.Write(file.Content, 0, file.Content.Length);
            }
        }

        archiveFile = archiveStream.ToArray();
    }

    return archiveFile;
}
```

Very simple, right?

Here is a sample Console Application in C# **.Net Core**.

```csharp
using System;
using System.IO;
using System.IO.Compression;

namespace ConsoleApp1
{
    class InMemoryFile
    {
        public string FileName { get; set; }

        public byte[] Content { get; set; }
    }

    class Program
    {
        static void Main(string[] args)
        {
            using var fs = File.OpenRead(@"C:\Users\rmauro\Pictures\cool-computer.jpg");

            var files = new[]
            {
                LoadFromFile(@"C:\Users\rmauro\Pictures\cool-computer.jpg"),
                LoadFromFile(@"C:\Users\rmauro\Pictures\34778.jpg"),
            };
            var result = GetZipArchive(files);

            using var fw = File.OpenWrite(@"C:\Users\rmauro\Pictures\out.zip");

            using var memZip = new MemoryStream(result);
            memZip.CopyTo(fw);

            fw.Close();

            Console.ReadKey();
        }

        static InMemoryFile LoadFromFile(string path)
        {
            using var fs = File.OpenRead(path);
            using var memFile = new MemoryStream();
            fs.CopyTo(memFile);

            memFile.Seek(0, SeekOrigin.Begin);

            return new InMemoryFile() { Content = memFile.ToArray(), FileName = Path.GetFileName(path) };
        }

        public static byte[] GetZipArchive(params InMemoryFile[] files)
        {
            byte[] archiveFile;
            using (var archiveStream = new MemoryStream())
            {
                using (var archive = new ZipArchive(archiveStream, ZipArchiveMode.Create, true))
                {
                    foreach (var file in files)
                    {
                        var zipArchiveEntry = archive.CreateEntry(file.FileName, CompressionLevel.Fastest);

                        using var zipStream = zipArchiveEntry.Open();
                        zipStream.Write(file.Content, 0, file.Content.Length);
                    }
                }

                archiveFile = archiveStream.ToArray();
            }

            return archiveFile;
        }
    }
}
```



