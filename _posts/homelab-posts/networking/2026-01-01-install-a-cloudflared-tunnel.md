---
layout: post
title: Install a Cloudflared tunnel
categories: [homelab, networking]
tags: [cloudflare, cloudclared, container, linux, proxmox]
after-content: [disclaimer-notice.html]
---

```apt install curl -Y```

# Add cloudflare gpg key
~~~
mkdir -p --mode=0755 /usr/share/keyrings
curl -fsSL https://pkg.cloudflare.com/cloudflare-main.gpg | tee /usr/share/keyrings/cloudflare-main.gpg >/dev/null
~~~

# Add this repo to your apt repositories
~~~
echo 'deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflared any main' | tee /etc/apt/sources.list.d/cloudflared.list
~~~

# install cloudflared
```apt update && apt install cloudflared```

# install cloudflared service
```cloudflared service install cloudflaretoken```

# run cloudflare tunnel
```cloudflared tunnel run --token cloudflaretoken```