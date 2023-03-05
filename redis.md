Redis Installation Guide
========================

This document describes how to install Redis on Ubuntu-18.04.x.

# Versions

- **Redis** 4.0.9.

# Installation Steps

- Install an instance:

        $ sudo apt update
        $ sudo apt install redis-server
        $ redis-cli info | grep 'redis_version'


Edit the file `/etc/redis/redis.conf` to setting port and add redis to systemd startup management (To enable the service after power on):

        ...
        port 6378
        supervised systemd
        ...

- (**optional**) Edit the file `/etc/redis/redis.conf` to bind all network interfaces:

        ...
        bind 0.0.0.0
        ...

- Start the service to apply it:

        $ sudo systemctl start redis.service

- Verify that Redis has started successfully:

	       $ sudo systemctl status redis.service
	       $ ss -tunelp | grep 6378

# Firewall

To open the following ports for internal usage:

- **6378** (and other ports of instances): for Redis.

# Authentication

- Edit the file `/etc/redis/redis.conf` and modify the following content:

        ...
        requirepass "<password>"
        ...

- Restart the service to apply it:

        $ sudo systemctl restart redis.service

