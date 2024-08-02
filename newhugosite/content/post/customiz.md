+++
date = 2021-05-21T22:33:47Z
description = ""
draft = true
slug = "customiz"
title = "Adding oh-my-bash on Windows Terminal"

+++


Oh-My-Bash is a styling set for your terminal and open source to manage your settings in `bash`. It comes with a ton of useful functions, help, plugins, themes, and more! If you've heard about **[oh-my-bash](https://github.com/ohmybash/oh-my-bash)** before, this project is a fork of it, but in Bourne Again Shell.

> Oh My Bash is an open source, community-driven framework for managing your [`bash`](https://www.gnu.org/software/bash/) configuration.

{{< bookmark url="https://github.com/ohmybash/oh-my-bash" title="ohmybash/oh-my-bash" description="A delightful community-driven framework for managing your bash configuration, and an auto-update tool so that makes it easy to keep up with the latest updates from the community. - ohmybash/oh-my-bash" icon="https://github.githubassets.com/favicons/favicon.svg" author="ohmybash" publisher="GitHub" thumbnail="https://opengraph.githubassets.com/75d2fcd47e5b0eef9af7d7b0686be780aef2b87ff34d3ad8708c219df7f92dc2/ohmybash/oh-my-bash" caption="" >}}

This look and feel is what we're looking for.

{{< figure src="https://res-2.cloudinary.com/dgoa5llvh/image/upload/q_auto/v1/ghost-blog-images/image.png" caption="Windows Terminal with oh-my-bash" >}}

## Installing zsh

To install Oh My Bash, you can choose between `cURL` or GNU `wget`, see the commands below:

> If you do not have cURL installed, use your distribution's package manager, eg sudo emerge curl # For Gentoo and similar

Via cURL:

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"
```

Via wget:

```bash
bash -c "$(wget https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh -O -)"
```

The output should be something like this:

```bash
rmauro@user-machine ~ $ sh -c "$(curl -fsSL https://raw.github.com/ohmybash/oh-my-bash/master/tools/install.sh)"
Cloning Oh My Bash...
Cloning into '/home/marcos/.oh-my-bash'...
remote: Enumerating objects: 257, done.
remote: Counting objects: 100% (257/257), done.
remote: Compressing objects: 100% (174/174), done.
remote: Total 257 (delta 17), reused 223 (delta 17), pack-reused 0
Receiving objects: 100% (257/257), 355.64 KiB | 170.00 KiB/s, done.
Resolving deltas: 100% (17/17), done.
Looking for an existing bash config...
Found ~/.bashrc. Backing up to ~/.bashrc.pre-oh-my-bash
Using the Oh My Bash template file and adding it to ~/.bashrc
         __                          __               __  
  ____  / /_     ____ ___  __  __   / /_  ____ ______/ /_ 
 / __ \/ __ \   / __ `__ \/ / / /  / __ \/ __ `/ ___/ __ \
/ /_/ / / / /  / / / / / / /_/ /  / /_/ / /_/ (__  ) / / /
\____/_/ /_/  /_/ /_/ /_/\__, /  /_.___/\__,_/____/_/ /_/ 
                        /____/                            .... is now installed!
Please look over the ~/.bashrc file to select plugins, themes, and options
To keep up on the latest news and updates, follow us on GitHub: https://github.com/ohmybash/oh-my-bash
[Oh My Bash] Would you like to check for updates? [Y/n]: \c
y

```

## Installing missing Powerline Fonts

Your terminal now should appear with broken font.

{{< figure src="https://res-2.cloudinary.com/dgoa5llvh/image/upload/q_auto/v1/ghost-blog-images/image.png" caption="Terminal with broken font" >}}

We need to install the Powerline fonts in our Windows fonts directory

Open a windows terminal, such as `powershell` and clone the `powerline/fonts` repository.

```powershell
git clone https://github.com/powerline/fonts.git

```

Open an `PowerShell` with admin permissions and navigate to the root of the repo and run:

```powershell
.\install.ps1

```

> If you get error of "execution of scripts is disabled on this system" check out this [stackoverflow answer](https://stackoverflow.com/a/4038991/1652594) (make sure to reverse the policy after).

Now we have the terminal fonts installed

