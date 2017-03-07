netdata has a [freeipmi](https://www.gnu.org/software/freeipmi/) plugin.

> FreeIPMI provides in-band and out-of-band IPMI software based on the IPMI v1.5/2.0 specification. The IPMI specification defines a set of interfaces for platform management and is implemented by a number vendors for system management. The features of IPMI that most users will be interested in are sensor monitoring, system event monitoring, power control, and serial-over-LAN (SOL).

## compile `freeipmi.plugin`

1. install `libipmimonitoring-dev` or `libipmimonitoring-devel` using the package manager of your system.

2. re-install netdata from source. The installer will detect that the required libraries are now available and will also build `freeipmi.plugin`.

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
