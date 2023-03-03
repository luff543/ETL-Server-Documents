ETL Server Installation Guide
========================

This document describes how to install ETL Server on Ubuntu-18.04.x.

# Server Versions

- **api-creator-server** 2.0.0 [install document](api-creator-server.md).
- **api-server-client** 2.0.0 [install document](api-server-client.md).

# Requirements

- **ssh**: OpenSSH 7.6p1 [install document](ssh.md).
- **MongoDB**: 3.6.23 [install document](mongodb.md).
- **RabbitMQ** 3.11.9 [install document](rabbitmq.md).
- **Redis** 4.0.9 [install document](redis.md).
- **Node.js** 16.14.0 [install document](node.md).
- **NPM** 8.3.1 [install document](node.md).
- **Nginx** 1.14.0 [install document](nginx.md).
- **Java** 11.0.18 [install document](java.md).
- (Option)**Python** 3.8.16/3.6.9 [install document](python.md).

# Host Settings

- Install [chrome](https://www.ubuntuupdates.org/package/google_chrome/stable/main/base/google-chrome-stable?id=202706&page=2) and chromium-chromedriver

        $ VERSION_STRING="109.0.5414.74-1"
        $ wget "https://dl.google.com/linux/chrome/deb/pool/main/g/google-chrome-stable/google-chrome-stable_${VERSION_STRING}_amd64.deb"
        $ sudo dpkg -i "google-chrome-stable_${VERSION_STRING}_amd64.deb"
        $ sudo apt install chromium-chromedriver

# Python Program Settings

- Robot Scraper requirements

        $ pip install robotframework
        $ pip install robotframework-selenium2library

