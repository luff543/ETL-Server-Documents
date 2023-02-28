Python Installation Guide
========================

This document describes how to install Python on Ubuntu-18.04.x.

# Versions

- **Python** 3.8.16.
- **Python** 3.6.9.


# Install Supporting Software

- Install **software-properties-common**.

        $ sudo apt install software-properties-common

# Installation Steps

- Install python:

        $ sudo add-apt-repository ppa:deadsnakes/ppa
        $ sudo apt update
        $ sudo apt install python3.8
        $ sudo apt install python3

- Add Python 3.6 & Python 3.8 to update-alternatives:

        $ sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 1
        $ sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.8 2

- Update Python 3 for point to Python 3.8:

        $ sudo update-alternatives --config python3

- Verify that Python install successfully:

        $ python --version

