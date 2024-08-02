+++
categories = ["SSH", "security"]
date = 2022-09-04T19:24:30Z
description = ""
draft = true
image = "http://res-5.cloudinary.com/dgoa5llvh/image/upload/q_auto/delicious.png"
slug = "change-default-port-of-ssh-server"
tags = ["SSH", "security"]
title = "Change Default Port 22 of SSH Server"

+++


**Port 22** is used by default to establish an SSH connections. This port is automatically configured during the installation of your operating system.

To reduce the number of brute force attacks, you can change it to a different port for SSH access.

### Changing the Default Port

To change the SSH port:

* Log on to the server as an **root** or an **administrator user**.
* Open the SSH configuration file **sshd_config** with the text editor **nano**:

```bash
$ nano /etc/ssh/sshd_config
```

* Search for the entry **Port 22** (could be commented)
* Replace it with the desired value - port between **1024** and **65536**
* Save and Restart the **OpenSSH** service

```bash
$ service ssh restart

# or 

$ systemctl restart sshd
```



