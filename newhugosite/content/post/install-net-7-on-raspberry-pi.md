+++
date = 2022-11-13T20:21:17Z
description = ""
draft = true
slug = "install-net-7-on-raspberry-pi"
title = "Install .NET 7 on Raspberry PI"

+++




The Raspberry Pi is a little single-board computer that is well-known for its adaptability and low price. It can be applied to a variety of applications, including media centers, home automation, robotics, and programming. We will walk you through the process of installing.NET 3.1 on Raspberry Pi OS, the operating system created specifically for Raspberry Pi, in this blog post.

## ⚡Installation Process

To install .NET 7 on a Raspberry Pi OS, we need to update the operating system, install some dependencies, download and install the .NET SDK, set environment variables, and verify the installation. The process involves opening the terminal and running several commands, which we have outlined in detail in the previous section.

### Step 1 Update our existing packages

```bash
sudo apt update && sudo apt upgrade
```

### Step 2

Download and run the **Microsoft Installation Script**

```bash
curl -sSL https://dot.net/v1/dotnet-install.sh | bash
```

Once finished we need

```bash
sudo chmod +x ./dotnet-install.sh

./dotnet-install.sh -c Current
```

## 😎 In Conclusion

Installing .NET 3.1 on Raspberry Pi OS may seem daunting, especially if you're new to Linux-based systems. However, by following the steps outlined in this blog post, you should be able to install .NET 3.1 on your Raspberry Pi with ease. The Raspberry Pi is a versatile and affordable computer that can be used for a wide range of projects, and the installation of .NET 3.1 on Raspberry Pi OS will enable you to develop and run applications on your Raspberry Pi.

```bash
curl -sSL https://dot.net/v1/dotnet-install.sh | bash
```

> Reference to execute on [https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-install-script](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-install-script)

Set as executable

```bash
sudo chmod +x ./dotnet-install.sh

./dotnet-install.sh -c Current
```



