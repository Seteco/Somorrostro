Netdata can be a very efficient real-time monitoring solution for IoT devices.

Here are a few tricks to get the most of it:

## 1. Disable External plugins

External plugins can consume more system resources than the netdata server. Try disabling them.

Edit `/etc/netdata/netdata.conf`, find the `[plugins]` section and disable all you don't need:

```
[plugins]
	# tc = yes
	# idlejitter = yes
	# proc = yes
	# cgroups = yes
	# checks = no
	# plugins directory = /usr/libexec/netdata/plugins.d
	# enable running new plugins = yes
	# check for new plugins every = 60
	# apps = yes
	# charts.d = yes
	# node.d = yes
```

In detail:

- `tc` is used for monitoring network QoS (tc classes)
- `idlejitter` is an internal filter (written in C) that attempts show if the systems starves for CPU. Disabling it will eliminate a thread.
- `cgroups` is used for monitoring linux containers. Most probably you are not going to need it. This will also eliminate another thread.
- `checks` is a debugging plugin, which is disabled by default.
- `proc` this is the internal plugin used to monitor the system. Normally, you don't want to disable this. You can disable individual functions of it below.
- `apps` is a plugin that monitors system processes. It is very complex and heavy (heavier than the netdata daemon), so if you don't need to monitor the processes running, you can disable it.
- `charts.d` is used for running BASH plugins (squid, nginx, mysql, etc). This is again a heavy plugin.
- `node.d` is a node.js plugin, currently used for SNMP data collection and monitoring named (the name server).

For most IoT devices, you can disable all plugins except `proc`. For `proc` there is another section that controls which functions of it you need:

```
[plugin:proc]
	# /proc/net/dev = yes
	# /proc/diskstats = yes
	# /proc/net/snmp = yes
	# /proc/net/snmp6 = yes
	# /proc/net/netstat = yes
	# /proc/net/stat/conntrack = yes
	# /proc/net/ip_vs/stats = yes
	# /proc/net/stat/synproxy = yes
	# /proc/stat = yes
	# /proc/meminfo = yes
	# /proc/vmstat = yes
	# /proc/net/rpc/nfsd = yes
	# /proc/sys/kernel/random/entropy_avail = yes
	# /proc/interrupts = yes
	# /proc/softirqs = yes
	# /proc/loadavg = yes
	# /sys/kernel/mm/ksm = yes
	# netdata server resources = yes
```

In this section you can select which modules of the `proc` plugin you need. All these are run in a single thread, one after another.

## 2. Disable logs

Normally, you will not need them. To disable them, set:

```
[global]
	debug log = none
	error log = none
	access log = none
```

## 3. Set memory mode to RAM

Setting the memory mode to `ram` will disable loading and saving the round robin database. This will not affect anything while running netdata, but it might be required if you have very limited storage available.

```
[global]
	memory mode = ram
```


## 4. CPU utilization

If after disabling the plugins you don't need, netdata still uses a lot of CPU without any clients accessing the dashboard, try lowering its data collection frequency. Going from "once per second" to "once every two seconds" will not have a significant difference, but it will cut the CPU resources required in half.

To set the update frequency, edit `/etc/netdata/netdata.conf` and set:

```
[global]
      update every = 2
```

You may have to increase this to 5 or 10 if the CPU of the device is weak.

## 5. Lower memory requirements

You can set the default size of the round robin database for all charts, using:

```
[global]
      history = 600
```

The units for history is `[global].update every` seconds. So if `[global].update every = 6` and `[global].history = 600`, you will have an hour of data.

