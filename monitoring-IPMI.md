netdata has a [freeipmi](https://www.gnu.org/software/freeipmi/) plugin.

> FreeIPMI provides in-band and out-of-band IPMI software based on the IPMI v1.5/2.0 specification. The IPMI specification defines a set of interfaces for platform management and is implemented by a number vendors for system management. The features of IPMI that most users will be interested in are sensor monitoring, system event monitoring, power control, and serial-over-LAN (SOL).

## compile `freeipmi.plugin`

1. install `libipmimonitoring-dev` or `libipmimonitoring-devel` using the package manager of your system.

2. re-install netdata from source. The installer will detect that the required libraries are now available and will also build `freeipmi.plugin`.

Keep in mind IPMI requires root access, so the plugin is setuid to root.

## netdata use

The plugin creates (up to) 8 charts, based on the information collected from IPMI:

1. number of sensors by state
2. number of events in SEL
3. Temperatures CELCIUS
4. Temperatures FAHRENHEIT
5. Voltages
6. Currents
7. Power
8. Fans


It also adds 2 alarms:

1. Sensors in non-nominal state (i.e. warning and critical)
2. SEL is non empty

![image](https://cloud.githubusercontent.com/assets/2662304/23674138/88926a20-037d-11e7-89c0-20e74ee10cd1.png)

The plugin does a speed test when it starts, to find out the duration needed by the IPMI processor to respond. Depending on the speed of your IPMI processor, charts may need several seconds to show up on the dashboard.

## `freeipmi.plugin` configuration

The plugin supports a few options. To see them, run:

```sh
# /usr/libexec/netdata/plugins.d/freeipmi.plugin -h
netdata freeipmi.plugin 1.5.0-539-gf17e83b_rolling
Usage:

  freeipmi.plugin [OPTIONS]

Available options:
  NUMBER, sets the data collection frequency
  debug, enables verbose output
  sel, enable SEL collection (it is on by default)
  no-sel, disable SEL collection
  hostname X, sets the remote host to connect to
  sdr-cache-dir X, sets the directory to save SDR cache files
  sensor-config-file X, set the filename to read sensor configuration
```

You can set these options in `/etc/netdata/netdata.conf` at this section:

```
[plugin:freeipmi]
	update every = 5
	command options = 
```

The minimum `update every` is 5. IPMI is slow and CPU hungry. So, once every 5 seconds is pretty acceptable.


## kipmi0 CPU usage

There have been reports that kipmi is showing increased CPU when the IPMI is queried.

[IBM has given a few explanations](http://www-01.ibm.com/support/docview.wss?uid=nas7d580df3d15874988862575fa0050f604).

Check also [this stackexchange post](http://unix.stackexchange.com/questions/74900/kipmi0-eating-up-to-99-8-cpu-on-centos-6-4)

To lower the CPU consumption of the system you can issue this command:

```sh
echo 10 > /sys/module/ipmi_si/parameters/kipmid_max_busy_us
```

This instructs the the kernel IPMI module to pause for a tick between checking IPMI. Querying IPMI will be a lot slower now (e.g. 10 seconds for IPMI to respond), but `kipmi` will not use any noticeable CPU. You can also use a higher number (this is the number of microseconds to poll IPMI for a response, before waiting for a tick).

If you need to disable IPMI for netdata, edit `/etc/netdata/netdata.conf` and set:

```
[plugins]
    freeipmi = no
```
