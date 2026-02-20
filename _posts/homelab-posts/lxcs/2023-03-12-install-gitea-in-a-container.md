---
layout: post
title: Install Gitea in a Container
categories: [homelab, lxcs]
tags: [container, gitea, linux, proxmox]
after-content: [disclaimer-notice.html]
---
Use Alpine Linux to create a proxmox container.

First, we need to install gitea and nano (because nano is the best text editor).

```apk add gitea nano```

If you try to start gitea, you will notice the error that it cannot run as root. Let's add a new user called "gitea".

```adduser -g "gitea" gitea```

Now that we have a non-root user created, let's switch user to that user.

```su gitea```

Now, we can run gitea.

```gitea```

This will start Gitea. You can browse to the IP address you assigned the proxmox container on port 3000 to access Gitea.

If you want to change the port Gitea listens on from 3000 to 80, you can edit the app.ini file located at ```/etc/gitea/app.ini```.

You need to change the HTTP_PORT from 3000 to 80 (or the port you want Gitea to listen on).

As a last step, I want to get the proxmox container to start gitea when it restarts. That takes us back to our first problem, that gitea needs to be run as gitea. Here are the commands to set nano as the text editor that will be used for crontab and to configure the proxmox container to switch user to gitea and start gitea after each reboot.

~~~
export VISUAL=nano
crontab -e
@reboot su gitea -c "/usr/bin/gitea"
~~~