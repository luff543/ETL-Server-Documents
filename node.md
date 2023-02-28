Node.js\/NPM Installation Guide
========================

This document describes how to install Node.js\/NPM on Ubuntu-18.04.x.

# Versions

- **Node.js** 16.14.0.
- **NPM** 8.3.1.

# Installation Steps

- Install script
```
$ wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
$ export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

- Install

        $ nvm install 16.14.0
        $ nvm use v16.14.0

- Uninstall

        $ nvm uninstall 16.14.0


