---
layout: post
title: Setup haproxy for servers
categories: [homelab, lxcs]
tags: [proxmox, linux, container, haproxy]
after-content: [disclaimer-notice.html]
---
## Networking
~~~
eth0 - VLAN 1: 192.168.1.100
eth1 - VLAN 12: 192.168.12.100
~~~
## DNS
First we need to create DNS A records for the FQDNs that we are going to use to access our web applications. These need to resolve to the IP Address assigned to the reverse proxy manager's network interface that is on the same VLAN as the device from which we will access the web applications. In this case, we are using a computer on VLAN 1 to access our web applications, so these records need to resolve to the IP Address assigned to the VLAN 1 network interface configured on the reverse proxy manager.

This will direct our traffic to the reverse proxy manager which we are going to configure to reverse proxy requests to the application servers that are connected to VLAN 12 without allowing VLAN 1 clients to have unrestricted access to VLAN 12 or needing a firewall (although a firewall may be a more secure option than a reverse proxy depending on your needs).

| Record Type | Record | Record Value |
| A | ns.example.com | 192.168.1.100 |
| A | status.example.com | 192.168.1.100 |

## Reverse Proxy
Next, we will install haproxy.
~~~
apt update
apt install -y haproxy
nano /etc/haproxy/haproxy.cfg
~~~
### Reverse Proxy Configuration
The haproxy configuration below listens on all available IP Addresses on ports 80 and 443 for requests. It is setup to offer SSL termination so you can install a wildcard SSL certificate on it and then you can connect to your web application servers through the proxy over TLS. Those TLS sessions will terminate at the haproxy server. The advantage is you can use a wildcard SSL certificate to use for all of the web application servers behind haproxy. The disadvantage to this approach is that since the TLS connections terminate at the haproxy server, there is no TLS encryption of the traffic between the haproxy server and the web application servers.
~~~
defaults
  log global
  option httplog
  option dontlognull
  timeout connect 5000ms
  timeout client 50000ms
  timeout server 50000ms

frontend http_front
  bind *:80
  bind *:443 ssl crt /etc/haproxy/certs.pem
  option forwardfor
  http-request add-header X-Forwarded-Proto https if { ssl_fc }

  # Redirect HTTP to HTTPS
  http-request redirect scheme https if !{ ssl_fc }

  # Route based on domain name
  use_backend pihole-backend if { hdr(host) -i ns.example.com }
  use_backend uptime-kuma-backend if { hdr(host) -i status.example.com }

backend pihole-backend
        mode http
        server house-pihole 192.168.12.2:8080 check

backend uptime-kuma-backend
        mode http
        server uptime-kuma 192.168.12.3:3001 check
~~~