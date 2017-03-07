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
