Netdata can be a very efficient real-time monitoring solution for IoT devices. It can serve as both a data collection agent, but also as a standalone dashboard you can use by directly accessing the dashboard on the device.

The latest versions of netdata have been significantly optimized to lower the CPU resources required. However, the plethora of plugins and technologies used for them, may be inappropriate for weak IoT devices.

Here are a few tricks to control the resources consumed by netdata programs:

## 1. Disable External plugins

External plugins can consume more system resources than the netdata server. Disable the ones you don't need.

Edit `/etc/netdata/netdata.conf`, find the `[plugins]` section:

```
[plugins]
	proc = yes

	tc = no
	idlejitter = no
	cgroups = no
	checks = no
	apps = no
	charts.d = no
	node.d = no

	plugins directory = /usr/libexec/netdata/plugins.d
	enable running new plugins = no
	check for new plugins every = 60
```

In detail:

- `proc` this is the internal plugin used to monitor the system. Normally, you don't want to disable this. You can disable individual functions of it below.
- `tc` is used for monitoring network QoS (tc classes)
- `idlejitter` is an internal filter (written in C) that attempts show if the systems starves for CPU. Disabling it will eliminate a thread.
- `cgroups` is used for monitoring linux containers. Most probably you are not going to need it. This will also eliminate another thread.
- `checks` is a debugging plugin, which is disabled by default.
- `apps` is a plugin that monitors system processes. It is very complex and heavy (heavier than the netdata daemon), so if you don't need to monitor the processes running, you can disable it.
- `charts.d` is used for running BASH plugins (squid, nginx, mysql, etc). This is again a heavy plugin.
- `node.d` is a node.js plugin, currently used for SNMP data collection and monitoring named (the name server).

For most IoT devices, you can disable all plugins except `proc`. For `proc` there is another section that controls which functions of it you need. Check the next section.

---

## 2. Disable internal plugins

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

In this section you can select which modules of the `proc` plugin you need. All these are run in a single thread, one after another. Still each one needs some RAM.

---

## 3. Disable logs

Normally, you will not need them. To disable them, set:

```
[global]
	debug log = none
	error log = none
	access log = none
```

---

## 4. Set memory mode to RAM

Setting the memory mode to `ram` will disable loading and saving the round robin database. This will not affect anything while running netdata, but it might be required if you have very limited storage available.

```
[global]
	memory mode = ram
```

---

## 5. CPU utilization

If after disabling the plugins you don't need, netdata still uses a lot of CPU without any clients accessing the dashboard, try lowering its data collection frequency. Going from "once per second" to "once every two seconds" will not have a significant difference, but it will cut the CPU resources required in half.

To set the update frequency, edit `/etc/netdata/netdata.conf` and set:

```
[global]
      update every = 2
```

You may have to increase this to 5 or 10 if the CPU of the device is weak.

Keep in mind this will also force dashboard chart refreshes to happen at the same rate. So increasing this number actually lowers data collection frequency but also lowers dashboard chart refreshes frequency.

This is a dashboard on a device with `[global].update every = 5` (this device is a media player and is now playing a movie):

![pi1](https://cloud.githubusercontent.com/assets/2662304/15338489/ca84baaa-1c88-11e6-9ab2-118208e11ce1.gif)


---

## 6. Lower memory requirements

You can set the default size of the round robin database for all charts, using:

```
[global]
      history = 600
```

The units for history is `[global].update every` seconds. So if `[global].update every = 6` and `[global].history = 600`, you will have an hour of data ( 6 x 600 = 3.600 ), which will store 600 points per dimension, one every 6 seconds.

---

## 7. Disable gzip compression of responses

Disabling gzip compression will not make a significant difference in performance, but it will save some CPU cycles while charts are refreshed. You can disable it, like this:

```
[global]
	enable web responses gzip compression = no
```

---

Finally, if no web server is installed on your device, you can use port tcp/80 for netdata:

```
[global]
	port = 80
```