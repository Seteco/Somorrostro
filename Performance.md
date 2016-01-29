# Netdata Performance

Netdata performance is affected by:

**Data collection**
- the number of charts for which data are collected
- the number of plugins running
- the technology of the plugins (i.e. BASH plugins are slower than binary plugins)
- the frequency of data collection

**Web clients accessing the data**
- the duration of the charts in the dashboard
- the number of charts refreshes requested

---

## Netdata Daemon

For most server systems, with a few hundred charts and a few thousand dimensions, the netdata daemon, without any web clients accessing it, should not use more than 2% of a single core.

In embedded systems, if the netdata daemon is using a lot of CPU without any web clients accessing it, you should lower the data collection frequency. To set the data collection frequency, edit `/etc/netdata/netdata.conf` and set `update_every` to a higher number (this is the frequency in seconds data are collected for all charts: higher number of seconds = lower frequency, the default is 1 for per second data collection). You can also set this frequency per module or chart. Check the **[[Configuration]]** section.

## Plugins

If a plugin is using a lot of CPU, you should lower its update frequency, or if you wrote it, re-factor it to be more CPU efficient. Check **[[External Plugins]]** for more details on writing plugins.

## CPU consumption when web clients are accessing dashboards

Netdata is very efficient when servicing web clients. On most server platforms, netdata should be able to serve 300+ web client requests per second per core for random chart durations.

Normally, each user connected will request less than 10 chart refreshes per second (the page may have hundreds of charts, but only the visible are refreshed). So you can expect 30 users per CPU core accessing dashboards before having any delays.

Netdata runs with the lowest possible process priority, so even if 1000 users are accessing dashboards, it will not influence your applications. CPU utilization will reach 100%, but your applications should get all the CPU they need.


## Monitoring a heavy loaded system

Netdata does not depend on disk I/O (apart its log files and `access.log` is written with buffering enabled). Some plugins that need disk may stop and show gaps during heavy system load, but the netdata daemon itself should be able to work and collect values from `/proc` and `/sys` and serve web clients accessing it.

