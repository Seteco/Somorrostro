![image10](https://cloud.githubusercontent.com/assets/2662304/14253729/534c6f9c-fa95-11e5-8243-93eb0df719aa.gif)


## Great! You are going to install netdata!

Before you start, make sure you have `zlib` (compression), `uuid` (GUIDs) and `libmnl` (iptables/netfilter) development files installed.

You also need to have a basic build environment in place. You will need packages like
`git`, `make`, `gcc`, `autoconf`, `autogen`, `automake`, `pkg-config`, etc.

---

### 1. Prepare your system

This is how to install the required packages on different distributions:

```sh
# Debian / Ubuntu
apt-get install zlib1g-dev uuid-dev libmnl-dev gcc make git autoconf autogen automake pkg-config

# Centos / Fedora / Redhat
yum install zlib-devel uuid-devel libmnl-devel gcc make git autoconf autogen automake pkgconfig

```

##### Arch Linux

The latest released version of netdata is already available via `pacman`. This will install the latest released version of netdata (no need to do anything more):

```sh
pacman -S netdata
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

---

#### additional optional packages you might need

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