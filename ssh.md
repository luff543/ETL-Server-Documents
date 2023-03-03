SSH Installation Guide
========================

This document describes how to install ssh on Ubuntu-18.04.x.

# Versions

- OpenSSH 7.6p1.

# Installation Steps

- Install ssh:

        $ sudo apt install openssh-server

- Start the service to apply it:

        $ sudo systemctl start ssh

- Verify that Redis has started successfully:

	       $ sudo systemctl status ssh
	       $ ss -tunelp | grep 22

- To enable the service after power on:

	       $ sudo systemctl enable ssh

- To disable the service after power on:

	       $ sudo systemctl disable ssh

- Stop  the service:

        $ sudo systemctl stop ssh


# Configurations

- Edit the file `/etc/ssh/sshd_config` and edit the following items:

        ...
        PermitRootLogin no
        PasswordAuthentication yes
        ...
        Port 22
        ...
