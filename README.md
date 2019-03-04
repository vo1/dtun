# DTUN docker tunneler

## Installation
Clone repo anywhere, e.g. /opt/dt
```
$ sudo apt-get install screen net-tools docker-compose
$ sudo chown :docker /etc/hosts && chmod g=rw /etc/hosts
$ echo "# DTUNHostsStart" >> /etc/hosts
$ echo "# DTUNHostsEnd" >> /etc/hosts
```
Adjusts paths in dt executable file to point to your projects dir and to installation path of DTUN
(default /opt/dt and /projects)

## Usage

**dt init projectname** - initializes docker project in $DTUNHome/data

**dt start projectname** - starts it

**dt rl** - reloads hosts file with running DTUN images
