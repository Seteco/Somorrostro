# Netdata Performance

Netdata performance is affected by:

**Data collection**
- the number of charts for which data are collected
- the number of plugins running
- the technology of the plugins (i.e. BASH plugins are slower than binary plugins)
- the frequency of data collection

You can control all the above.

**Web clients accessing the data**
- the duration of the charts in the dashboard
- the number of charts refreshes requested
- the compression level of the web responses

---

## Netdata Daemon

For most server systems, with a few hundred charts and a few thousand dimensions, the netdata daemon, without any web clients accessing it, should not use more than 1% of a single core.

To prove netdata scalability, check issue [#1323](https://github.com/firehol/netdata/issues/1323#issuecomment-265501668) where netdata collects 95.000 metrics per second, with 12% CPU utilization of a single core!

In embedded systems, if the netdata daemon is using a lot of CPU without any web clients accessing it, you should lower the data collection frequency. To set the data collection frequency, edit `/etc/netdata/netdata.conf` and set `update_every` to a higher number (this is the frequency in seconds data are collected for all charts: higher number of seconds = lower frequency, the default is 1 for per second data collection). You can also set this frequency per module or chart. Check the **[[Configuration]]** section.

## Plugins

If a plugin is using a lot of CPU, you should lower its update frequency, or if you wrote it, re-factor it to be more CPU efficient. Check **[[External Plugins]]** for more details on writing plugins.

## CPU consumption when web clients are accessing dashboards

Netdata is very efficient when servicing web clients. On most server platforms, netdata should be able to serve **1800 web client requests per second per core** for auto-refreshing charts.

Normally, each user connected will request less than 10 chart refreshes per second (the page may have hundreds of charts, but only the visible are refreshed). So you can expect 180 users per CPU core accessing dashboards before having any delays.

Netdata runs with the lowest possible process priority, so even if 1000 users are accessing dashboards, it should not influence your applications. CPU utilization will reach 100%, but your applications should get all the CPU they need.

To lower the CPU utilization of netdata when clients are accessing the dashboard, set `web compression level = 1`, or disable web compression completely by setting `enable web responses gzip compression = no`. Both settings are in the `[web]` section.


## Monitoring a heavy loaded system

Netdata, while running, does not depend on disk I/O (apart its log files and `access.log` is written with buffering enabled and can be disabled). Some plugins that need disk may stop and show gaps during heavy system load, but the netdata daemon itself should be able to work and collect values from `/proc` and `/sys` and serve web clients accessing it.

Keep in mind that netdata saves its database when it exits and loads it back when restarted. While it is running though, its DB is only stored in RAM and no I/O takes place for it.


## Running netdata in embedded devices

Embedded devices usually have very limited CPU resources available, and in most cases, just a single core.

We suggest to do the following:

#### external plugins

 `charts.d.plugin` and `apps.plugin`, each consumes twice the CPU resources of the netdata daemon.

 If you don't need them, disable them (edit `/etc/netdata/netdata.conf` and search for the plugins section).

 If you need them, increase their `update every` value (again in `/etc/netdata/netdata.conf`), so that they do not run that frequently.

#### internal plugins

If netdata is still using a lot of CPU, lower its update frequency. Going from per second updates, to once every 2 seconds updates, will cut the CPU resources of all netdata programs **in half**, and you will still have very frequent updates.

If the CPU of the embedded device is too weak, try setting even lower update frequency. Experiment with `update every = 5` or `update every = 10` (higher number = lower frequency), until you get acceptable results.

#### Single threaded web server

Normally, netdata spawns a thread for each web client. This allows netdata to utilize all the available cores for servicing chart refreshes. You can however disable this feature and serve all charts one after another, using a single thread / core. This will might lower the CPU pressure on the embedded device. To enable the single threaded web server, edit `/etc/netdata/netdata.conf` and set `mode = single-threaded` in the `[web]` section.

## netdata process priority

in `netdata.conf`, there is:

```
[global]
    process scheduling policy = idle
```

Possible values are:

policy|description
:-----:|:--------
`idle`|use CPU only when there is spare - this is lower than nice 19 - this is the default for netdata and it is so low that netdata will run in "slow motion" under extreme system load, resulting in short (1-2 seconds) gaps at the charts.
`other`<br/>or<br/>`nice`|this is the default policy for all processes under Linux. It provides dynamic priorities based on the nice level of each process.
`batch`|This policy is similar to `other` in that it schedules the thread according to its dynamic priority (based on the nice value).  The difference is that this policy will cause the scheduler to always assume that the thread is CPU-intensive.  Consequently, the scheduler will  apply a small scheduling penalty with respect to wakeup behavior, so that this thread is mildly disfavored in scheduling decisions.
`fifo`|`fifo` can be used only with static priorities higher than 0, which means that when a `fifo` threads becomes runnable, it will always  immediately  preempt  any  currently running  `other`, `batch`, or `idle` thread.  `fifo` is a simple scheduling algorithm without time slicing.
`rr`|a simple enhancement of `fifo`.  Everything described above for `fifo` also applies to `rr`, except that each thread is allowed to run only for  a  maximum time  quantum.
`keep`<br/>or<br/>`none`|do not set scheduling policy, priority or nice level - i.e. keep running with whatever it is set already (e.g. by systemd).

For more information see `man sched`.

### scheduling priority

It can be set only for `rr` and `fifo`. Once the policy is set to one of these, the following will appear:

```
[global]
    process scheduling priority = 0
```

These priorities are usually from 0 to 99. Higher numbers make the process more important.

### nice level

It can only be set when the policy is set to `other`, `nice`, or `batch`. The following will appear:

```
[global]
    process nice level = 19
```

