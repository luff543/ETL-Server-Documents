MongoDB Installation Guide
==========================

This document describes how to install MongoDB on Ubuntu-18.04.x.

# Versions

- **MongoDB**: 3.6.23.

# Disk Partitions

Please make sure that you have prepare an extra disk partition for database to store data.

In this guide we assume that the partition is at `/data`.

You can use the same hard drive if you don't have other hard drive partitions.


# Optional Packages

- Install **net-tools** and **bash-completion** to ease to enter shell commands. This is optional:

        $ sudo apt install net-tools bash-completion

    - Remeber to re-login to apply bash-completion.

- You can install other packages for management.

# Installation Packages

note: If you want to install other versions, please refer to MongoDB [document](https://www.mongodb.com/docs/v3.6/tutorial/install-mongodb-on-ubuntu/).
- Import the public key used by the package management system:

        $ wget -qO - https://www.mongodb.org/static/pgp/server-3.6.asc | sudo apt-key add -

- Create a list file for MongoDB:

        $ echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/3.6 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list

- Reload local package database:

        $ sudo apt-get update

- Then install MongoDB:

        $ sudo apt-get install -y mongodb-org

or 

- Install a specific release of MongoDB:

        $ sudo apt-get install -y mongodb-org=3.6.23 mongodb-org-server=3.6.23 mongodb-org-shell=3.6.23 mongodb-org-mongos=3.6.23 mongodb-org-tools=3.6.23

- Pin a specific version of MongoDB:

        $ echo "mongodb-org hold" | sudo dpkg --set-selections
        $ echo "mongodb-org-server hold" | sudo dpkg --set-selections
        $ echo "mongodb-org-shell hold" | sudo dpkg --set-selections
        $ echo "mongodb-org-mongos hold" | sudo dpkg --set-selections
        $ echo "mongodb-org-tools hold" | sudo dpkg --set-selections

# Create MongoDB data folder

        $ sudo mkdir -p /data/mongodb
        $ sudo mkdir -p /data/mongodb/db
        $ sudo mkdir -p /data/mongodb/log
        $ sudo mkdir -p /data/mongodb/run

        $ sudo chown -R mongodb:mongodb /data/mongodb
        $ sudo chmod -R 755 /data/mongodb

# Configurations

- Edit the file `/etc/mongod.conf` and edit the following items:

        ...
        storage:
          dbPath: /data/mongodb/db
          journal:
          enabled: true
        ...
        systemLog:
          destination: file
          path: /data/mongodb/log/mongodb.log
        ...
        net:
          port: 27017
          bindIp: 0.0.0.0
        ...
        replication:
        ...

# Starting the Service

- To enable the service after power on:

        $ sudo systemctl enable mongod.service

- To start the MongoDB service:

        $ sudo systemctl start mongod

	- If you receive an error similar to the following when starting [`mongod`](https://www.mongodb.com/docs/v3.6/reference/program/mongod/#bin.mongod "bin.mongod"):

		`Failed to start mongod.service: Unit mongod.service not found.`
		
		Run the following command first:

	       $ sudo systemctl daemon-reload

- Verify that MongoDB has started successfully:

	       $ sudo systemctl status mongod
	       $ ss -tunelp | grep 27017


# Firewall

To open the following ports for internal usage:

- **27017**: for MongoDB.

        $ sudo ufw allow from <ip> to any port 27017
        $ sudo ufw allow 27017

# (Option)Authentication

- Create an administrator and users for source code:

        $ mongo
        > use admin
        > db.createUser({ user: 'admin', pwd: '<password>', roles: [ 'root' ] });
        > db.createUser({ user: '<user_name>', pwd: '<password>', roles: [ 'readWriteAnyDatabase' ] });

- Then edit the file `/etc/mongod.conf` and edit the following items:

        ...
       #security: 
       security.authorization : enabled
        ...

- Restart the service to apply authentication.

        $ sudo systemctl restart mongod.service

    - Set `authorization: "disable"` and restart the service to stop authentication.

# (Option)Replica Set

- Edit the file `/etc/mongod.conf` and edit the following items:

        ...
        replication:
          replSetName: "<replica_set_name>"
        ...

- Restart the service to apply replica set.

        $ sudo systemctl restart mongod.service

- Add a secondary node:

        $ mongo
        PRIMARY> rs.add('<host<:port>>')

- Or remove a secondary node:

        $ mongo
        PRIMARY> rs.remove('<host<:port>>')

### Replica Set with Authentication

- To generate the key file:

        $ openssl rand -base64 741 > mongodb-keyfile

- Copy the file `mongodb-keyfile` to the servers.

- Edit the file `/etc/mongod.conf` and edit the following items:

        ...
        security:
          keyFile: "/<path_of_keyfile>/mongodb-keyfile"
          authorization: "enabled"
        ...

- Restart the service to apply replica set with authentication.

        $ sudo systemctl restart mongod.service
