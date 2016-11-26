netdata collects system metrics by itself. It has many internal plugins, for collecting most of the metrics presented by default when it starts, collecting data from `/proc`, `/sys` and other Linux kernel sources.

To collect non-system metrics, netdata supports a plugin architecture. You can add custom plugins following the [External Plugins Guide](https://github.com/firehol/netdata/wiki/External-Plugins).

The following are the currently available external plugins:

- **[Web Servers](#web-servers)**, such as apache, nginx, tomcat
- **[Database Servers](#database-servers)**, such as mysql, mariadb, postgres
- **[Social Sharing Servers](#social-sharing-servers)**, like retroshare
- **[Proxy Servers](#proxy-servers)**, like squid
- **[Name Servers](#name-servers)** (DNS), like bind
- **[UPS and Power](#ups-and-power)**, such as APC UPS, NUT, SMA WebBox (solar power)
- **[Mail Servers](#mail-servers)**, like postfix, exim
- **[System](#system)**, for processes and other system metrics
- **[Sensors](#sensors)**, like temperature, fans speed, voltage, humidity
- **[Network](#network)**, such as SNMP devices, access points
- **[Telephony Servers](#telephony-servers)**, like openSIPS
- **[Skeleton Plugins](#skeleton-plugins)**, for writing your own data collectors


---

## Web Servers

application|language|notes|
:---------:|:------:|:----|
apache|python<br/>v2 or v3|Connects to multiple apache servers (local or remote) to collect real-time performance metrics.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [apache.chart.py](https://github.com/firehol/netdata/blob/master/python.d/apache.chart.py)<br/>configuration file: [python.d/apache.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/apache.conf)|
apache|BASH<br/>Shell Script|Connects to an apache server (local or remote) to collect real-time performance metrics.<br/><br/>DEPRECATED IN FAVOR OF THE PYTHON ONE. It is still supplied only as an example module to shell scripting plugins.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [apache.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/apache.chart.sh)<br/>configuration file: [charts.d/apache.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/apache.conf)|
apache_cache|python<br/>v2 or v3|Monitors one or more apache `cache.log` files to provide real-time cache performance statistics.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [apache_cache.chart.py](https://github.com/firehol/netdata/blob/master/python.d/apache_cache.chart.py)<br/>configuration file: [python.d/apache_cache.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/apache_cache.conf)|
ipfs|python<br/>v2 or v3|Connects to multiple ipfs servers (local or remote) to collect real-time performance metrics.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [ipfs.chart.py](https://github.com/firehol/netdata/blob/master/python.d/ipfs.chart.py)<br/>configuration file: [python.d/ipfs.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/ipfs.conf)|
nginx|python<br/>v2 or v3|Connects to multiple nginx servers (local or remote) to collect real-time performance metrics.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [nginx.chart.py](https://github.com/firehol/netdata/blob/master/python.d/nginx.chart.py)<br/>configuration file: [python.d/nginx.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/nginx.conf)|
nginx|BASH<br/>Shell Script|Connects to an nginx server (local or remote) to collect real-time performance metrics.<br/><br/>DEPRECATED IN FAVOR OF THE PYTHON ONE. It is still supplied only as an example module to shell scripting plugins.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [nginx.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/nginx.chart.sh)<br/>configuration file: [charts.d/nginx.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/nginx.conf)|
nginx_log|python<br/>v2 or v3|Monitors one or more nginx log files to collect real-time pageviews per response status.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [nginx_log.chart.py](https://github.com/firehol/netdata/blob/master/python.d/nginx_log.chart.py)<br/>configuration file: [python.d/nginx_log.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/nginx_log.conf)|
phpfpm|python<br/>v2 or v3|Connects to multiple phpfpm servers (local or remote) to collect real-time performance metrics.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [phpfpm.chart.py](https://github.com/firehol/netdata/blob/master/python.d/phpfpm.chart.py)<br/>configuration file: [python.d/phpfpm.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/phpfpm.conf)|
phpfpm|BASH<br/>Shell Script|Connects to a phpfpm server (local or remote) to collect real-time performance metrics.<br/><br/>DEPRECATED IN FAVOR OF THE PYTHON ONE. It is still supplied only as an example module to shell scripting plugins.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [phpfpm.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/phpfpm.chart.sh)<br/>configuration file: [charts.d/phpfpm.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/phpfpm.conf)|
tomcat|python<br/>v2 or v3|Connects to multiple tomcat servers (local or remote) to collect real-time performance metrics.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [tomcat.chart.py](https://github.com/firehol/netdata/blob/master/python.d/tomcat.chart.py)<br/>configuration file: [python.d/tomcat.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/tomcat.conf)|
tomcat|BASH<br/>Shell Script|Connects to a tomcat server (local or remote) to collect real-time performance metrics.<br/><br/>DEPRECATED IN FAVOR OF THE PYTHON ONE. It is still supplied only as an example module to shell scripting plugins.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [tomcat.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/tomcat.chart.sh)<br/>configuration file: [charts.d/tomcat.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/tomcat.conf)|


---

## Database Servers

application|language|notes|
:---------:|:------:|:----|
memcached|python<br/>v2 or v3|Connects to multiple memcached servers (local or remote) to collect real-time performance metrics.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [memcached.chart.py](https://github.com/firehol/netdata/blob/master/python.d/memcached.chart.py)<br/>configuration file: [python.d/memcached.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/memcached.conf)|
mysql|python<br/>v2 or v3|Connects to multiple mysql servers (local or remote) to collect real-time performance metrics.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [mysql.chart.py](https://github.com/firehol/netdata/blob/master/python.d/mysql.chart.py)<br/>configuration file: [python.d/mysql.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/mysql.conf)|
mysql|BASH<br/>Shell Script|Connects to multiple mysql servers (local or remote) to collect real-time performance metrics.<br/><br/>DEPRECATED IN FAVOR OF THE PYTHON ONE. It is still supplied only as an example module to shell scripting plugins.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [mysql.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/mysql.chart.sh)<br/>configuration file: [charts.d/mysql.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/mysql.conf)|
postgres|python<br/>v2 or v3|Connects to multiple postgres servers (local or remote) to collect real-time performance metrics.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [postgres.chart.py](https://github.com/firehol/netdata/blob/master/python.d/postgres.chart.py)<br/>configuration file: [python.d/postgres.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/postgres.conf)|
redis|python<br/>v2 or v3|Connects to multiple redis servers (local or remote) to collect real-time performance metrics.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [redis.chart.py](https://github.com/firehol/netdata/blob/master/python.d/redis.chart.py)<br/>configuration file: [python.d/redis.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/redis.conf)|


---

## Social Sharing Servers

application|language|notes|
:---------:|:------:|:----|
retroshare|python<br/>v2 or v3|Connects to multiple retroshare servers (local or remote) to collect real-time performance metrics.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [retroshare.chart.py](https://github.com/firehol/netdata/blob/master/python.d/retroshare.chart.py)<br/>configuration file: [python.d/retroshare.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/retroshare.conf)|


---

## Proxy Servers

application|language|notes|
:---------:|:------:|:----|
squid|python<br/>v2 or v3|Connects to multiple squid servers (local or remote) to collect real-time performance metrics.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [squid.chart.py](https://github.com/firehol/netdata/blob/master/python.d/squid.chart.py)<br/>configuration file: [python.d/squid.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/squid.conf)|
squid|BASH<br/>Shell Script|Connects to a squid server (local or remote) to collect real-time performance metrics.<br/><br/>DEPRECATED IN FAVOR OF THE PYTHON ONE. It is still supplied only as an example module to shell scripting plugins.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [squid.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/squid.chart.sh)<br/>configuration file: [charts.d/squid.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/squid.conf)|


---

## Name Servers

application|language|notes|
:---------:|:------:|:----|
named|node.js|Connects to multiple named (ISC-Bind) servers (local or remote) to collect real-time performance metrics. All versions of bind after 9.9.10 are supported.<br/>&nbsp;<br/>netdata plugin: [node.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/node.d.plugin)<br/>plugin module: [named.node.js](https://github.com/firehol/netdata/blob/master/node.d/named.node.js)<br/>configuration file: [node.d/named.conf](https://github.com/firehol/netdata/blob/master/conf.d/node.d/named.conf)|


---

## UPS and Power

application|language|notes|
:---------:|:------:|:----|
apcupsd|BASH<br/>Shell Script|Connects to an apcupsd server to collect real-time statistics of an APC UPS.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [apcupsd.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/apcupsd.chart.sh)<br/>configuration file: [charts.d/apcupsd.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/apcupsd.conf)|
nut|BASH<br/>Shell Script|Connects to a nut server (upsd) to collect real-time UPS statistics.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [nut.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/nut.chart.sh)<br/>configuration file: [charts.d/nut.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/nut.conf)|
sma_webbox|node.js|Connects to multiple remote SMA webboxes to collect real-time performance metrics of the photovoltaic (solar) power generation.<br/>&nbsp;<br/>netdata plugin: [node.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/node.d.plugin)<br/>plugin module: [sma_webbox.node.js](https://github.com/firehol/netdata/blob/master/node.d/sma_webbox.node.js)<br/>configuration file: [node.d/sma_webbox.conf](https://github.com/firehol/netdata/blob/master/conf.d/node.d/sma_webbox.conf)|


## Mail Servers

application|language|notes|
:---------:|:------:|:----|
dovecot|python<br/>v2 or v3|Connects to multiple dovecot servers (local or remote) to collect real-time performance metrics.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [dovecot.chart.py](https://github.com/firehol/netdata/blob/master/python.d/dovecot.chart.py)<br/>configuration file: [python.d/dovecot.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/dovecot.conf)|
exim|python<br/>v2 or v3|Charts the exim queue size.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [exim.chart.py](https://github.com/firehol/netdata/blob/master/python.d/exim.chart.py)<br/>configuration file: [python.d/exim.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/exim.conf)|
exim|BASH<br/>Shell Script|Charts the exim queue size.<br/><br/>DEPRECATED IN FAVOR OF THE PYTHON ONE. It is still supplied only as an example module to shell scripting plugins.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [exim.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/exim.chart.sh)<br/>configuration file: [charts.d/exim.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/exim.conf)|
postfix|python<br/>v2 or v3|Charts the postfix queue size (supports multiple queues).<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [postfix.chart.py](https://github.com/firehol/netdata/blob/master/python.d/postfix.chart.py)<br/>configuration file: [python.d/postfix.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/postfix.conf)|
postfix|BASH<br/>Shell Script|Charts the postfix queue size.<br/><br/>DEPRECATED IN FAVOR OF THE PYTHON ONE. It is still supplied only as an example module to shell scripting plugins.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [postfix.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/postfix.chart.sh)<br/>configuration file: [charts.d/postfix.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/postfix.conf)|


---

## System

application|language|notes|
:---------:|:------:|:----|
apps|C|`apps.plugin` collects resource usage statistics for all processes running in the system. It groups the entire process tree and reports dozens of metrics for CPU utilization, memory footprint, disk I/O, swap memory, network connections, open files and sockets, etc. It reports metrics for application groups, users and user groups.<br/>&nbsp;<br/>netdata plugin: [apps_plugin.c](https://github.com/firehol/netdata/blob/master/src/apps_plugin.c)<br/>configuration file: [apps_groups.conf](https://github.com/firehol/netdata/blob/master/conf.d/apps_groups.conf)|
cpu_apps|BASH<br/>Shell Script|Collects the CPU utilization of select apps.<br/><br/>DEPRECATED IN FAVOR OF `apps.plugin`. It is still supplied only as an example module to shell scripting plugins.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [cpu_apps.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/cpu_apps.chart.sh)<br/>configuration file: [charts.d/cpu_apps.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/cpu_apps.conf)|
load_average|BASH<br/>Shell Script|Collects the current system load average.<br/><br/>DEPRECATED IN FAVOR OF THE NETDATA INTERNAL ONE. It is still supplied only as an example module to shell scripting plugins.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [load_average.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/load_average.chart.sh)<br/>configuration file: [charts.d/load_average.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/load_average.conf)|
mem_apps|BASH<br/>Shell Script|Collects the memory footprint of select applications.<br/><br/>DEPRECATED IN FAVOR OF `apps.plugin`. It is still supplied only as an example module to shell scripting plugins.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [mem_apps.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/mem_apps.chart.sh)<br/>configuration file: [charts.d/mem_apps.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/mem_apps.conf)|


---

## Sensors

application|language|notes|
:---------:|:------:|:----|
cpufreq|python<br/>v2 or v3|Collects the current CPU frequency from `/sys/devices`.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [cpufreq.chart.py](https://github.com/firehol/netdata/blob/master/python.d/cpufreq.chart.py)<br/>configuration file: [python.d/cpufreq.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/cpufreq.conf)|
cpufreq|BASH<br/>Shell Script|Collects current CPU frequency from `/sys/devices`.<br/><br/>DEPRECATED IN FAVOR OF THE PYTHON ONE. It is still supplied only as an example module to shell scripting plugins.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [cpufreq.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/cpufreq.chart.sh)<br/>configuration file: [charts.d/cpufreq.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/cpufreq.conf)|
hddtemp|python<br/>v2 or v3|Connects to multiple hddtemp servers (local or remote) to collect real-time performance metrics.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [hddtemp.chart.py](https://github.com/firehol/netdata/blob/master/python.d/hddtemp.chart.py)<br/>configuration file: [python.d/hddtemp.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/hddtemp.conf)|
hddtemp|BASH<br/>Shell Script|Connects to a hddtemp server (local or remote) to collect real-time performance metrics.<br/><br/>DEPRECATED IN FAVOR OF THE PYTHON ONE. It is still supplied only as an example module to shell scripting plugins.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [hddtemp.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/hddtemp.chart.sh)<br/>configuration file: [charts.d/hddtemp.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/hddtemp.conf)|
sensors|BASH<br/>Shell Script|Collects sensors values from files in `/sys`.<br/><br/>DEPRECATED IN FAVOR OF THE PYTHON ONE. It is still supplied only as an example module to shell scripting plugins.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [sensors.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/sensors.chart.sh)<br/>configuration file: [charts.d/sensors.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/sensors.conf)|
sensors|python<br/>v2 or v3|Uses `lmsensors` to collect sensor data.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [sensors.chart.py](https://github.com/firehol/netdata/blob/master/python.d/sensors.chart.py)<br/>configuration file: [python.d/sensors.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/sensors.conf)|


---

## Network

application|language|notes|
:---------:|:------:|:----|
ap|BASH<br/>Shell Script|Uses the `iw` command to provide statistics of wireless clients connected to a wireless access point running on this host (works well with `hostapd`).<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [ap.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/ap.chart.sh)<br/>configuration file: [charts.d/ap.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/ap.conf)|
fping|C|Charts network latency statistics for any number of nodes, using the `fping` command. A recent (probably unreleased) version of fping is required. The plugin supplied can install it in `/usr/local`.<br/>&nbsp;<br/>netdata plugin: [fping.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/fping.plugin) (this is a shell wrapper to start fping - once fping is started, netdata and fping communicate directly - it can also install the right version of fping)<br/>configuration file: [fping.conf](https://github.com/firehol/netdata/blob/master/conf.d/fping.conf)|
snmp|node.js|Connects to multiple snmp servers to collect real-time performance metrics.<br/>&nbsp;<br/>netdata plugin: [node.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/node.d.plugin)<br/>plugin module: [snmp.node.js](https://github.com/firehol/netdata/blob/master/node.d/snmp.node.js)<br/>configuration file: [node.d/snmp.conf](https://github.com/firehol/netdata/blob/master/conf.d/node.d/snmp.conf)|


---

## Telephony Servers

application|language|notes|
:---------:|:------:|:----|
opensips|BASH<br/>Shell Script|Connects to an opensips server (local only) to collect real-time performance metrics.<br/><br/>DEPRECATED IN FAVOR OF THE PYTHON ONE. It is still supplied only as an example module to shell scripting plugins.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [opensips.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/opensips.chart.sh)<br/>configuration file: [charts.d/opensips.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/opensips.conf)|


---

## Skeleton Plugins

application|language|notes|
:---------:|:------:|:----|
example|BASH<br/>Shell Script|Skeleton plugin in BASH.<br/><br/>DEPRECATED IN FAVOR OF THE PYTHON ONE. It is still supplied only as an example module to shell scripting plugins.<br/>&nbsp;<br/>netdata plugin: [charts.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/charts.d.plugin)<br/>plugin module: [example.chart.sh](https://github.com/firehol/netdata/blob/master/charts.d/example.chart.sh)<br/>configuration file: [charts.d/example.conf](https://github.com/firehol/netdata/blob/master/conf.d/charts.d/example.conf)|
example|python<br/>v2 or v3|Skeleton plugin in Python.<br/>&nbsp;<br/>netdata plugin: [python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/python.d.plugin)<br/>plugin module: [example.chart.py](https://github.com/firehol/netdata/blob/master/python.d/example.chart.py)<br/>configuration file: [python.d/example.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/example.conf)|

