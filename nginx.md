Nginx Installation Guide
========================

This document describes how to install Nginx on Ubuntu-18.04.x.

# Versions

- **Nginx** 1.14.0.

# Installation Steps

- Install an instance:

        $ sudo apt update
        $ sudo apt install nginx
        $ nginx -v


Edit the file `/etc/nginx/nginx.conf` to setting proxy pass:

```
...
http {
...
	server {
	  listen 8001;
	
	  location ~ ^/(api|public)(.*)$ {
		add_header Access-Control-Allow-Origin *;
		# 把 host、IP 等 header 都一起送給反向代理的 server
		proxy_set_header Host      $host;
		proxy_set_header X-Real-IP $remote_addr;
		# 反向代理到同一台主機的 7001 Port
		proxy_pass http://127.0.0.1:7001;
		proxy_read_timeout 600s;
	  }
	
	  location ~ ^/(.*)$ {
		add_header Access-Control-Allow-Origin *;
		# 把 host、IP 等 header 都一起送給反向代理的 server
		proxy_set_header Host      $host;
		proxy_set_header X-Real-IP $remote_addr;
		# 反向代理到同一台主機的 3001 Port
		proxy_pass http://127.0.0.1:3001;
		proxy_read_timeout 600s;
	  }
	}
...
}
...
```

- Check whether the config is set correctly

        $ sudo nginx -t -c /etc/nginx/nginx.conf

- Start the service to apply it:

        $ sudo systemctl start nginx

- Verify that Redis has started successfully:

	       $ sudo systemctl status nginx
	       $ ss -tunelp | grep 8001


# Firewall

To open the following ports for internal usage:

- **8001** (and other ports of instances): for api-creator-server and api-server-client.

        $ sudo ufw allow 8001


