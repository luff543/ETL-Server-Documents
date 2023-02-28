api-creator-server Installation Guide
========================

This document describes how to install api-creator-server on Ubuntu-18.04.x.

# Versions

- **api-creator-server** 2.0.0.

# Requirements

- **Node.js** 16.14.0.
- **NPM** 8.3.1.

# Installation Steps

- Use TextCrawler3 fix somthing code

        Replace amqp://user:password@localhost:7878 to amqp://<user>:<password>@localhost:5672

- Upload project to /home/<user>/nodejs

- Install

        $ cd /home/<user>/nodejs/api-creator-server
        $ nvm use v16.14.0
        $ npm install

# Configurations

- Edit the api-creator-server config file `config/config.default.js` and edit the following items:

        ...
        config.apiDomain = 'http://<public ip>:8001';
        ...

- Edit the api-creator-server config file `config/config.prod.js` and edit the following items:

        ...
        config.apiDomain = 'http://<public ip>:8001';
        ...

# Starting the Service

- Confirm executable commands

         $ cd /home/<user>/nodejs/api-creator-server
        $ npm run

- if you don't want to run in schedule, type:

        $ npm run no-schedule

- if you wan't to test with schedule, type:

        $ npm run prod


- Verify that api-creator-server has started successfully:

	       $ ss -tunelp | grep 7001