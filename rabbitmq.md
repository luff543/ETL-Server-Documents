RabbitMQ Installation Guide
===========================

This document describes how to install RabbitMQ on Ubuntu-18.04.x.

# Versions

- **Erlang** OTP 25.2.3 with Erlang.
- **RabbitMQ** 3.11.9.

# Optional Packages

- Install  package commands. This is optional:

        $ sudo apt install net-tools bash-completion git wget make apt-transport-https

    - Remeber to re-login to apply bash-completion.

- You can install other packages for management.


# Install Erlang and RabbitMQ

- Install RabbitMQ:

```
#!/usr/bin/sh
sudo apt-get install curl gnupg apt-transport-https -y
## Team RabbitMQ's main signing key
curl -1sLf "https://keys.openpgp.org/vks/v1/by-fingerprint/0A9AF2115F4687BD29803A206B73A36E6026DFCA" | sudo gpg --dearmor | sudo tee /usr/share/keyrings/com.rabbitmq.team.gpg > /dev/null
## Launchpad PPA that provides modern Erlang releases
curl -1sLf "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0xf77f1eda57ebb1cc" | sudo gpg --dearmor | sudo tee /usr/share/keyrings/net.launchpad.ppa.rabbitmq.erlang.gpg > /dev/null
## PackageCloud RabbitMQ repository
curl -1sLf "https://packagecloud.io/rabbitmq/rabbitmq-server/gpgkey" | sudo gpg --dearmor | sudo tee /usr/share/keyrings/io.packagecloud.rabbitmq.gpg > /dev/null
## Add apt repositories maintained by Team RabbitMQ
sudo tee /etc/apt/sources.list.d/rabbitmq.list <<EOF
## Provides modern Erlang/OTP releases
##
## "bionic" as distribution name should work for any reasonably recent Ubuntu or Debian release.
## See the release to distribution mapping table in RabbitMQ doc guides to learn more.
deb [signed-by=/usr/share/keyrings/net.launchpad.ppa.rabbitmq.erlang.gpg] http://ppa.launchpad.net/rabbitmq/rabbitmq-erlang/ubuntu bionic main
deb-src [signed-by=/usr/share/keyrings/net.launchpad.ppa.rabbitmq.erlang.gpg] http://ppa.launchpad.net/rabbitmq/rabbitmq-erlang/ubuntu bionic main
## Provides RabbitMQ
##
## "bionic" as distribution name should work for any reasonably recent Ubuntu or Debian release.
## See the release to distribution mapping table in RabbitMQ doc guides to learn more.
deb [signed-by=/usr/share/keyrings/io.packagecloud.rabbitmq.gpg] https://packagecloud.io/rabbitmq/rabbitmq-server/ubuntu/ bionic main
deb-src [signed-by=/usr/share/keyrings/io.packagecloud.rabbitmq.gpg] https://packagecloud.io/rabbitmq/rabbitmq-server/ubuntu/ bionic main
EOF
## Update package indices
sudo apt-get update -y
## Install Erlang packages
sudo apt-get install -y erlang-base \
                        erlang-asn1 erlang-crypto erlang-eldap erlang-ftp erlang-inets \
                        erlang-mnesia erlang-os-mon erlang-parsetools erlang-public-key \
                        erlang-runtime-tools erlang-snmp erlang-ssl \
                        erlang-syntax-tools erlang-tftp erlang-tools erlang-xmerl
## Install rabbitmq-server and its dependencies
sudo apt-get install rabbitmq-server -y --fix-missing
```


- (**TLS optional**) Put the files **cert.crt**, **key.crt** into `/etc/rabbitmq` directory, then edit the file `/etc/rabbitmq/rabbitmq.config` to support TLS v1.1 and v1.2:

        [
          {rabbit, [
            {ssl_listeners, [5671]},
            {ssl_options, [{certfile, "/etc/rabbitmq/cert.crt"},
                           {keyfile, "/etc/rabbitmq/key.crt"},
                           {versions, ['tlsv1.2', 'tlsv1.1']}]}
          ]}
        ].

    - Or you can use the new RabbitMQ configuration file format by editing `etc/rabbitmq/rabbitmq.conf` (this file will be ignored if **rabbitmq.config** exists):

            listeners.ssl.default = 5671
            ssl_options.certfile = /etc/rabbitmq/cert.crt
            ssl_options.keyfile = /etc/rabbitmq/key.crt
            ssl_options.versions.1 = tlsv1.2
            ssl_options.versions.2 = tlsv1.1

- To enable the service after power on:

        $ sudo systemctl enable rabbitmq-server.service

- To start the RabbitMQ service:

        $ sudo systemctl start rabbitmq-server.service

- Verify that RabbitMQ has started successfully:

	       $ sudo systemctl status rabbitmq-server.service
	       $ ss -tunelp | grep 5672

- To enable the management plugin:

        $ sudo rabbitmq-plugins enable rabbitmq_management

- To enable the management plugin:
        $ sudo systemctl status rabbitmq-server.service
        $ ss -tunelp | grep 5672
        $ ss -tunelp | grep 15672

# Create a Default Admin for Remote Management

- To add an administrator **admin** with password **admin** (later to use UI to change the password):

        $ sudo rabbitmqctl add_user admin <password>; \
          sudo rabbitmqctl set_user_tags admin administrator; \
          sudo rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"

- Then login to the host `http://SERVER_IP:15672` with **admin** and change the password.

# How to Create New Users, Virtual Hosts and Set Permissions

- Add a user:

        $ sudo rabbitmqctl add_user <username> <password>

- Add a virtual host:

        $ sudo rabbitmqctl add_vhost <vhost>

- Set permissions:

        $ sudo rabbitmqctl set_permissions [-p <vhost>] <username> <conf> <write> <read>

# Firewall

To open the following ports for internal usage:

- **5672**: for AMQP.
- **5671**: (**optional**) for AMQP TLS.
- **4369** and **25672**: (**optional**) for RabbitMQ cluster.
- **15672**: (**optional**) for management UI plugin.

        $ sudo ufw allow from <ip> to any port 5672
        $ sudo ufw allow 5672
        $ sudo ufw allow from <ip> to any port 15672
        $ sudo ufw allow 15672

# Clustering

- Assign the node name before joining a cluster.

- Copy `/var/lib/rabbitmq/.erlang.cookie` of the first node to other nodes.

- To join the first node:

        <node2>$ sudo rabbitmqctl [-n <node2_name>] stop_app
        <node2>$ sudo rabbitmqctl [-n <node2_name>] join_cluster <node1_name>
        <node2>$ sudo rabbitmqctl [-n <node2_name>] start_app

    - Example:

            rabbit2$ sudo rabbitmqctl stop_app
            rabbit2$ sudo rabbitmqctl join_cluster rabbit@rabbit1
            rabbit2$ sudo rabbitmqctl -n rabbit@rabbit2 start_app

- Or to remove from the cluster:

        <node2>$ sudo rabbitmqctl stop_app
        <node2>$ sudo rabbitmqctl reset
        <node2>$ sudo rabbitmqctl start_app

### High Availability (Mirrored Queue)

- Using policies:

        $ sudo rabbitmqctl set_policy <policy_name> [-p <vhost>] <pattern> <definitions_json> --apply-to queues

    - Example:

            $ sudo rabbitmqctl set_policy ha-test -p /test ".*" '{"ha-mode":"all"}' --apply-to queues
