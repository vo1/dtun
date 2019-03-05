# DTUN docker template builder for Magento2

## Installation
Clone repo anywhere, e.g. /opt/dt, symlink dt executable somewhere in PATH
```
$ sudo apt-get install screen net-tools docker-compose
$ sudo dt dt-setup
```
Adjust paths in dt executable file to point to your projects dir and to installation path of DTUN using env variables:

```
DTUN_HOME=/opt/dt
DTUN_PROJECTS=/projects
DTUN_EXT_INTERFACE=eth0 # only needed when reverse proxy is used
DTUN_USE_REVERSE_PROXY=1 # uses apache2 as reverse proxy
```

## Usage

**dt dt-setup** - initializes dt, sets proper permissions, adding required lines to sudoers, etc

**dt projectname init [--mysql=5.7] [--php=7.0]** - initializes docker project in $DTUNHome/data (defaults apache-php7.1 & mysql-5.6)

**dt projectname start** - starts it

**dt rl** - reloads hosts file with running DTUN images

**dt projectname mc** - starts mc in docker container (with sudo to application user)

**dt projectname bash** - same for bash

**dt projectname mysql** - starts mysql client

(see other commands in dt executable)
