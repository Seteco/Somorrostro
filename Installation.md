![image10](https://cloud.githubusercontent.com/assets/2662304/14253729/534c6f9c-fa95-11e5-8243-93eb0df719aa.gif)


## Great! You are going to install netdata!

You can install the latest release of netdata, using your package manager in

- Gentoo Linux (`sudo emerge --ask netdata`)
- Arch Linux (`sudo pacman -S netdata`)

For other systems, or if you want the latest unreleased version, please follow these 2 steps:

1. [Prepare your system](#1-prepare-your-system)

  Install the required packages on your system.

2. [Install netdata](#2-install-netdata)

  Download and install netdata. You can also update it the same way.

---

### 1. Prepare your system

Try our experimental automatic requirements installer (no need to be root). This will try to find the packages that should be installed on your system to build and run netdata.

Without `python-mysql` and `node.js`:

```sh
curl -Ss 'https://raw.githubusercontent.com/firehol/netdata-demo-site/master/install-required-packages.sh' >/tmp/kickstart.sh && bash /tmp/kickstart.sh netdata
```

With `python-mysql` (monitoring mysql/mariadb) and `node.js` (named monitoring and SNMP):

```sh
curl -Ss 'https://raw.githubusercontent.com/firehol/netdata-demo-site/master/install-required-packages.sh' >/tmp/kickstart.sh && bash /tmp/kickstart.sh netdata-all
```

If the above do not work for you, please open a github issue with a copy of the message you get on screen. We are trying to find out the variations out there.

---

This how to do it by hand:

```sh
# Debian / Ubuntu
apt-get install zlib1g-dev uuid-dev libmnl-dev gcc make git autoconf autogen automake pkg-config

# Centos / Fedora / Redhat
yum install zlib-devel libuuid-devel libmnl-devel gcc make git autoconf autogen automake pkgconfig

```

It would be nice (but not required) if you also install:

- `curl` (used by shell plugins to collect data from applications),
- `jq` (a JSON parser and query command line tool),
- `nodejs` (used for `node.js` plugins for monitoring `named` and SNMP devices)

You can install them using this:

```sh
# debian / ubuntu
apt-get install curl jq nodejs

# centos / fedora / redhat
yum install curl jq nodejs
```

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

Run these to have netdata managed by systemd:

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

In the system directory you can find `netdata-init-d`. Copy it to the proper place according to your distribution documentation.

#### openrc (gentoo)

In the `system` directory you can find `netdata-openrc`. Copy it to the proper place according to your distribution documentation.

#### centos

Check PR https://github.com/firehol/netdata/pull/403

#### other systems

You can start netdata by running it from `/etc/rc.local` or equivalent.

## log-rotation

The installer, when run as `root`, will install `/etc/logrotate.d/netdata`.

## Updating netdata after its installation

You can update netdata to the latest version by getting into `netdata.git` you downloaded before and running:

```sh
# go to the git downloaded directory
cd /path/to/git/downloaded/netdata

# download the latest version
git pull

# rebuild it, install it, run it
./netdata-installer.sh
```

The installer will also restart netdata with the new version.

---

## Starting netdata at boot

To start it at boot, just run `/usr/sbin/netdata` from your `/etc/rc.local` or equivalent.

You can also find systemd and openrc scripts in the **[system](https://github.com/firehol/netdata/tree/master/system)** directory.

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
