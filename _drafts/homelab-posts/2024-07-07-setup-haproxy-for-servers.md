apt update
apt install -y haproxy
nano /etc/haproxy/haproxy.cfg

defaults
        log     global
        mode    http
        option  httplog
        option  dontlognull
        timeout connect 5000
        timeout client  50000
        timeout server  50000
        errorfile 400 /etc/haproxy/errors/400.http
        errorfile 403 /etc/haproxy/errors/403.http
        errorfile 408 /etc/haproxy/errors/408.http
        errorfile 500 /etc/haproxy/errors/500.http
        errorfile 502 /etc/haproxy/errors/502.http
        errorfile 503 /etc/haproxy/errors/503.http
        errorfile 504 /etc/haproxy/errors/504.http


frontend house-pihole-frontend
        bind *:80
        default_backend pihole-backend

frontend uptime-kuma-frontend
	bind *:81
	default_backend uptime-kuma-backend

backend pihole-backend
        server house-pihole 192.168.1.2:80 check

backend uptime-kuma-backend
        server uptime-kuma 192.168.1.3:80 check