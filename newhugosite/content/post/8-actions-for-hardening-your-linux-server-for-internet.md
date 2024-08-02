+++
categories = ["Linux", "security", "SSH", "hardening", "Internet"]
date = 2022-09-11T20:57:43Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1629654291663-b91ad427698f?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwxMTc3M3wwfDF8c2VhcmNofDh8fGxpbnV4fGVufDB8fHx8MTY2MjkxNzcyNw&ixlib=rb-1.2.1&q=80&w=2000"
slug = "8-actions-for-hardening-your-linux-server-for-internet"
tags = ["Linux", "security", "SSH", "hardening", "Internet"]
title = "SSH Exposed to Internet - 8 steps check list"

+++


Exposing virtual machines to the internet it's not an easy task. Often managed using an SSH connection we must secure our machines as much as we can against hacker attacks.

Let's go through some often and ease configurations that will make it an attacker hard if trying to access your machine.

## 💬 In this issue

- [Creating a non-root user and use eventually elevated privileged](#creating-a-non-root-user)
- [Enabling UFW Uncomplicated Firewall](#enabling-ufw-uncomplicated-firewall)
- [Unattended Upgrades](#adding-unattended-upgrades)
- [Hardening the SSH Access](#hardening-the-ssh-configuration)
  - [Use SSH Keys instead of Password](#using-ssh-keys-instead-of-passwords)
  - [Change the default Port for SSH](#changing-default-port-for-ssh)
  - [Disable Password Login](#disable-password-authentication-over-ssh)
  - [Disable Root Login over SSH](#disable-root-user-login)
  - [Use *config* file in SSH client for easier connection](#using-ssh-config-file-on-ssh-client)

### 👉 Information we'll use in our lab

User: _root_Password:  generated with a _password generator_Description: Our **Root** userUser: _secondary_Password: generated with a _password generator_Description: Non **Root** user for daily basis accessOperating System: Ubuntu Server 20.04 - but it also working for different flavors of Linux.

---

## Creating a non-root user

First thing first: Never use the root user for non-privilege tasks. To accomplish that let's create a non-root user and use the **sudo** command to elevate our privileges only when necessary.

Logged as the **root** user let's create a non-root user called **secondary**. Execute the following command in our _bash shell._

```bash
# create a regular user with name secondary
$ adduser secondary
```

We should answer some questions (starting with the password) and we shall have the user.

{{< figure src="http://res-5.cloudinary.com/dgoa5llvh/image/upload/q_auto/1-728rufa7phmxmgy8f15w.png" caption="Creating the secondary user" >}}

Now we have a user with fewer privileges. The next step is to grant the use **sudo** command when necessary.

### Add to Sudo Group

Run `usermod`command to include **secondary** into **sudo** group.

```bash
# includes the secondary into sudo group
$ usermod -aG sudo secondary
```

Now we're able to use **sudo** in front of a command to run as administrative rights.

---

## Enabling UFW Uncomplicated Firewall

UFW is the default firewall system (under the hood is the great IPTables) in Ubuntu Server.With the use of **ufw** it's very simple to allow or block a port.

```bash
$ sudo ufw allow 22

```

Don't forget to use **sudo -** we should be using the **secondary** user instead of the **root**

```bash
$ sudo ufw default deny incoming
$ sudo ufw default allow outgoing
$ sudo ufw enable

```

Deny all incoming by default, allow outgoing, and finally enable the **ufw** service

```bash
$ sudo ufw status

```

{{< figure src="http://res-1.cloudinary.com/dgoa5llvh/image/upload/q_auto/2-50yzbzo1xuj2huan0o45.png" caption="Displaying Status of <em>ufw </em>Service" >}}

Great. Now we have **ufw** up and running.

---

## Adding Unattended Upgrades

Unattended-Upgrade is a service that regularly checks and updates the OS packages.

> The purpose of unattended-upgrades is to keep the computer current with the latest security (and other) updates automatically.[https://wiki.debian.org/UnattendedUpgrades](https://wiki.debian.org/UnattendedUpgrades)



```bash
$ sudo apt install unattended-upgrades

$ sudo systemctl status unattended-upgrades.service

```

After installing just enable the service and we should be good.

{{< figure src="http://res-3.cloudinary.com/dgoa5llvh/image/upload/q_auto/3-42id1z6idjks8bmqeb7c.png" caption="Unattended upgrades Status" >}}

The default configuration will automatically retrieve new package updates for most of the packages included in the Ubuntu repositories.

---

## Hardening the SSH Configuration

Most Linux servers are administered remotely using SSH using OpenSSH technology. Which is the default SSH server software used within Ubuntu.

This is one of the major vectors of attack by hackers on Linux exposed on the internet. Let's work on improving the configurations of our SSH server.

---

## Using SSH keys instead of passwords

SSH keys are the recommended way to log into any Linux server environment remotely.

Back in our client machine (not on the server) let's create a pair of keys - private and public keys. This also works on Windows 10/11.

```bash
$ ssh-keygen

```

You can specify a different location to save the keys if you like.

{{< figure src="http://res-3.cloudinary.com/dgoa5llvh/image/upload/q_auto/4-c8a57fo3adz85715isd4.png" caption="Generating the Public and Private Keys" >}}

The next step is to copy the SSH key to the Ubuntu server.

```bash
$ cat /home/{username}/.ssh/id_rsa.pub

```

Copy the content output to the transfer area.

_**Back into our server**_

```bash
# create .ssh if not created
$ sudo mkdir -p ~/.ssh

# copy the public key to authorized_keys file
$ sudo echo public_key_string >> ~/.ssh/authorized_keys

# remove all permissions on .ssh folder
$ sudo chmod -R go= ~/.ssh

# adding read permission to our user - our scenario is secondary - on .ssh folder
$ chown -R secondary:secondary ~/.ssh


```

Now we have the public key generated by our client machine in the server in the property location and with permissions in place.

This is it. Now we're able to log in using the recently created SSH Key. Let's try.

```bash
# ssh -i (indicates where is the key)
$ ssh -i $HOME/.ssh/id_rsa secondary@{SERVER-IP}
```

---

## Changing default Port for SSH

Open the SSH configuration file **sshd_config** with a text editor.

```bash
$ sudo nano /etc/ssh/sshd_config

```

Look for the entry `Port 22` - it could be commented on. Replace it with the desired value - port between 1024 and 65536

```
. . .
Port 2222
. . .
```

Save and Restart the OpenSSH service.

```bash
$ sudo systemctl restart ssh

```

---

## Disable Password Authentication over SSH

Change property to **PasswordAuthentication no**

```bash
. . .
PasswordAuthentication no
. . .

```

## Disable Root User Login

Change property to **PermitRootLogin no**

```bash
. . .
PermitRootLogin no
. . .

```

### Tweaking some other sshd_config Configurations

Open the config file **sshd_config** one more time

```bash
$ sudo nano /etc/ssh/sshd_config

```

```bash
. . .
MaxAuthTries 3
. . .

```

```bash
. . .
PermitEmptyPasswords no
. . .

```

Test and Restart the SSH Service to reflect the new configuration

```bash
# test the configuration changes
$ sudo sshd -t

# restart the service and load the new configuration
$ sudo systemctl restart ssh

```

---

## Using ssh config file on SSH Client

Adding a config file in the `.ssh` folder on the ssh client allows us to define the key, host, user, and other configurations by default per connection.

_**In our Client's machine**_

Create the **config** file in **~/.ssh/config** location

```bash
# THIS IS OUR CLIENT - NOT OUR UBUNTU SERVER
$ nano ~/.ssh/config

```

Past the following content.

```yaml
# connection name
Host ssh-server

   # host address (ip or hostname)
   Hostname 192.168.0.100
   
   # port of ssh server
   Port 2222

   # user
   User secondary

   # private key to use
   IdentityFile ~/.ssh/id_rsa_locaweb_secondary

```

Then we can connect using only the connection name

```bash
$ ssh ssh-server

```

---

## 😎 Final thoughts

Wow, finally we made it. That is enough!

Of course, we can keep going hardening it even more adding more protection - it never stops.

Share in the comments section what you would do to make it even better or different.

## ❤️ Enjoy this article?

**Forward to a friend** and let them know.



