![image10](https://cloud.githubusercontent.com/assets/2662304/14253729/534c6f9c-fa95-11e5-8243-93eb0df719aa.gif)


## Great! You are going to install netdata!

You can install the latest release of netdata, using your package manager in

- Arch Linux (`sudo pacman -S netdata`)
- Gentoo Linux (`sudo emerge --ask netdata`)

For other systems, or if you want the latest unreleased version, please follow these 2 steps:

1. [Prepare your system](#1-prepare-your-system)

  Install the required packages on your system.

2. [Install netdata](#2-install-netdata)

  Download and install netdata. You can also update it the same way.

---

### 1. Prepare your system

Try our experimental automatic requirements installer (no need to be root). This will try to find the packages that should be installed on your system to build and run netdata. It supports most major Linux distributions released after 2010:

- **Arch** Linux and its derivatives
- **Gentoo** Linux and its derivatives
- **Debian** Linux and its derivatives (including **Ubuntu**, **Mint**)
- **Fedora** and its derivatives (including **Red Hat Enterprise Linux**, **CentOS**)
- **SuSe** Linux and its derivatives (including **openSuSe**)

Install the packages for having a **basic netdata installation** (system monitoring and many applications, without  `mysql` / `mariadb`, `named`, hardware sensors and `SNMP`):

```sh
curl -Ss 'https://raw.githubusercontent.com/firehol/netdata-demo-site/master/install-required-packages.sh' >/tmp/kickstart.sh && bash /tmp/kickstart.sh netdata
```

Install all the required packages for **monitoring everything netdata can monitor**:

```sh
curl -Ss 'https://raw.githubusercontent.com/firehol/netdata-demo-site/master/install-required-packages.sh' >/tmp/kickstart.sh && bash /tmp/kickstart.sh netdata-all
```

If the above do not work for you, please [open a github issue](https://github.com/firehol/netdata/issues/new?title=packages%20installer%20failed&labels=installation%20help&body=The%20experimental%20packages%20installer%20failed.%0A%0AThis%20is%20what%20it%20says:%0A%0A%60%60%60txt%0A%0Aplease%20paste%20your%20screen%20here%0A%0A%60%60%60) with a copy of the message you get on screen. We are trying to make it work everywhere.

---

This is how to do it by hand:

```sh
# Debian / Ubuntu
apt-get install zlib1g-dev uuid-dev libmnl-dev gcc make git autoconf autoconf-archive autogen automake pkg-config curl

# Fedora
dnf install zlib-devel libuuid-devel libmnl-devel gcc make git autoconf autoconf-archive autogen automake pkgconfig curl

# CentOS / Red Hat Enterprise Linux
yum install zlib-devel libuuid-devel libmnl-devel gcc make git autoconf autoconf-archive autogen automake pkgconfig curl

```

Please note that for RHEL/CentOS you might need [EPEL](http://www.tecmint.com/how-to-enable-epel-repository-for-rhel-centos-6-5/).

Once netdata is compiled, to run it the following packages are required (already installed using the above commands):

package|description
:-----:|-----------
`libuuid`|part of `util-linux` for GUIDs management
`zlib`|gzip compression for the web server

*netdata will fail to start without the above.*

netdata plugins and various aspects of netdata can be enabled or benefit when these are installed (they are optional):

package|description
:-----:|-----------
`bash`|for shell plugins and alarm notifications
`curl`|for shell plugins and alarm notifications
`tc`|for monitoring Linux QoS
`python`|for most of the external plugins
`python-yaml`|for parsing `python.d.plugin` configuration
`python-mysqldb`|used for monitoring mysql databases
`nodejs`|used for `node.js` plugins for monitoring `named` and SNMP devices
`lm_sensors`|for monitoring hardware sensors
`libmnl`|for collecting netfilter metrics

*netdata will greatly benefit if you have the above packages installed.*

---

# 2. Install netdata

Do this to install and run netdata:

```sh

# download it - the directory 'netdata' will be created
git clone https://github.com/firehol/netdata.git --depth=1
cd netdata

# build it, install it, start it
./netdata-installer.sh

```

The script `netdata-installer.sh` will build netdata and install it to your system.

If you don't want to install it on the default directories, you can run the installer like this: `./netdata-installer.sh --install /opt`. This one will install netdata in `/opt/netdata`.

Once the installer completes, the file `/etc/netdata/netdata.conf` will be created (if you changed the installation directory, the configuration will appear in that directory too).

You can edit this file to set options. One common option to tweak is `history`, which controls the size of the memory database netdata will use. By default is `3600` seconds (an hour of data at the charts) which makes netdata use about 10-15MB of RAM (depending on the number of charts detected on your system). Check **[[Memory Requirements]]**.

To apply the changes you made, you have to restart netdata.

## starting netdata at boot

In the `system` directory you can find scripts and configurations for the various distros.

#### systemd

The installer already installs `netdata.service` if it detects a systemd system.

To install `netdata.service` by hand, run:

```sh
# stop netdata
killall netdata

# copy netdata.service to systemd
cp system/netdata.service /etc/systemd/system/

# let systemd know there is a new service
systemctl daemon-reload

# enable netdata at boot
systemctl enable netdata

# start netdata
service netdata start
```

#### init.d

In the system directory you can find `netdata-lsb`. Copy it to the proper place according to your distribution documentation. For Ubuntu, this can be done via running the following commands as root.

```sh
# copy the netdata startup file to /etc/init.d
cp system/netdata-lsb /etc/init.d/netdata

# make sure it is executable
chmod +x /etc/init.d/netdata

# enable it
update-rc.d netdata defaults
```

#### openrc (gentoo)

In the `system` directory you can find `netdata-openrc`. Copy it to the proper place according to your distribution documentation.

#### CentOS / Red Hat Enterprise Linux

For older versions of RHEL/CentOS that don't have systemd, an init script is included in the system directory. This can be installed by running the following commands as root.

```sh
# copy the netdata startup file to /etc/init.d
cp system/netdata-init-d /etc/init.d/netdata

# make sure it is executable
chmod +x /etc/init.d/netdata

# enable it
chkconfig --add netdata
```

_There have been some recent work on the init script, see PR https://github.com/firehol/netdata/pull/403_

#### other systems

You can start netdata by running it from `/etc/rc.local` or equivalent.

## log-rotation

The installer, when run as `root`, will install `/etc/logrotate.d/netdata`.

## Updating netdata after its installation

### Manual update

#### Method 1: netdata-updater.sh

`netdata-installer.sh` generates `netdata-updater.sh` upon any successful installation  
You can use this script to update your netdata installation with the same options you used to install it in the first place.

````sh
# go to the git downloaded directory
cd /path/to/git/downloaded/netdata

# run the updater
./netdata-updater.sh
````

_Netdata will be restarted with the new version._

#### Method 2: git pull

You can also update netdata to the latest version by hand, using this:

```sh
# go to the git downloaded directory
cd /path/to/git/downloaded/netdata

# download the latest version
git pull

# rebuild it, install it, run it
./netdata-installer.sh
```

_Netdata will be restarted with the new version._

### Auto-update

_Please, consider the risks of running an auto-update. Something can always go wrong. Keep an eye on your installation, and run a manual update if something ever fails._

You can call `netdata-updater.sh` from a cron-job. A successful update will not trigger an email from cron. 

````sh
# Edit your cron-jobs
crontab -e

# add a cron-job at the bottom. This one will update netdata every day at 6:00AM:
# update netdata
0 6 * * * /path/to/git/downloaded/netdata/netdata-updater.sh
````

---

## Working with netdata

- You can start netdata by executing it with `/usr/sbin/netdata` (the installer will also start it).

- You can stop netdata by killing it with `killall netdata`.
    You can stop and start netdata at any point. Netdata saves on exit its round robbin
    database to `/var/cache/netdata` so that it will continue from where it stopped the last time.

To access the web site for all graphs, go to:

 ```
 http://127.0.0.1:19999/
 ```

You can get the running config file at any time, by accessing `http://127.0.0.1:19999/netdata.conf`.

---

## Uninstalling netdata

The script `netdata-installer.sh` generates another script called `netdata-uninstaller.sh`.

To uninstall netdata, run:

```
cd /path/to/netdata.git
./netdata-uninstaller.sh --force
```

The uninstaller will ask you to confirm all deletions.

---

## Other Systems

We are trying to collect all the information about netdata package maintainers at [issue 651](https://github.com/firehol/netdata/issues/651). So, please have a look there for ASUSTOR NAS, OpenWRT, ReadyNAS, etc.

##### Alpine 3.4

Execute these commands to install netdata in Alpine Linux 3.4:

```
# install required packages
apk add alpine-sdk bash curl zlib-dev util-linux-dev libmnl-dev gcc make git autoconf automake pkgconfig python logrotate

# add the netdata user and group by had
# the installer uses the commands useradd and groupadd,
# but these are not available in alpine
addgroup netdata
adduser -D -G netdata netdata

# if you plan to run node.js netdata plugins
apk add nodejs

# make netdata start at boot
echo -e "#!/usr/bin/env bash\n/usr/sbin/netdata" >/etc/local.d/netdata.start
chmod 755 /etc/local.d/netdata.start

# make netdata stop at shutdown
echo -e "#!/usr/bin/env bash\nkillall netdata" >/etc/local.d/netdata.stop
chmod 755 /etc/local.d/netdata.stop

# enable the local service to start automatically
rc-update add local

# download netdata - the directory 'netdata' will be created
git clone https://github.com/firehol/netdata.git --depth=1
cd netdata

# build it, install it, start it
./netdata-installer.sh
```

##### Synology

Login into DSM

- Package Center > Settings > Package Sources
- Add http://packages.synocommunity.com/
- Community > Install Debian Chroot

ssh to diskstation as root

```sh
/var/packages/debian-chroot/scripts/start-stop-status chroot
apt-get install zlib1g-dev uuid-dev libmnl-dev gcc make git autoconf autogen automake pkg-config
```

continue install from this (chroot) prompt
