**General**
* [[Home]]
* [[Why netdata?]]
* **[[Installation]]**
* [[Command Line Options]]
* [[Configuration]]
* [[Log Files]]
* [[Tracing Options]]

---

**Running Netdata**
* [[Performance]]
* [[Memory Requirements]]
* [[netdata Security]]

Special Uses
* [[netdata for IoT]]<br/>lower netdata resource utilization
* [[high performance netdata]]<br/>netdata public on the internet

Notes on memory management
* [Memory deduplication](https://github.com/firehol/netdata/wiki/Memory-Deduplication---Kernel-Same-Page-Merging---KSM)<br/>half netdata memory requirements
* [[netdata virtual memory size]]

---

**Database Replication and Mirroring**

* [[Replication Overview]]
* [[monitoring ephemeral nodes]]<br/>Use netdata to monitor auto-scaled cloud servers.
* [[netdata proxies]]<br/>Streaming netdata metrics between netdata servers.

---

**Backends**
<br/>archiving netdata collected metrics to a time-series database

* [[netdata-backends]]<br/>`graphite`, `opentsdb`, `kairosdb`, `influxdb`, `elasticsearch`, `blueflood`
* [netdata with prometheus](https://github.com/firehol/netdata/wiki/Using-Netdata-with-Prometheus)

---

**Health monitoring - Alarms**
<br/>alarms and alarm notifications in netdata

* [Overview](https://github.com/firehol/netdata/wiki/health-monitoring)
* [Reference](https://github.com/firehol/netdata/wiki/health-configuration-reference)<br/>reference for writing alarms
* [Examples](https://github.com/firehol/netdata/wiki/health-configuration-examples)<br/>simple how-to for writing alarms
* [Notifications Configuration](https://github.com/firehol/netdata/wiki/alarm-notifications)<br/>
  - [web notifications](https://github.com/firehol/netdata/wiki/web-browser-notifications)
  - [emails](https://github.com/firehol/netdata/wiki/email-notifications)
  - [discordapp.com](https://github.com/firehol/netdata/wiki/discord-notifications)
  - [messagebird.com](https://github.com/firehol/netdata/wiki/messagebird-notifications)
  - [pagerduty.com](https://github.com/firehol/netdata/wiki/pagerduty-notifications)
  - [pushbullet.com](https://github.com/firehol/netdata/wiki/pushbullet-notifications)
  - [pushover.net](https://github.com/firehol/netdata/wiki/pushover-notifications)
  - [slack.com](https://github.com/firehol/netdata/wiki/slack-notifications)
  - [telegram.org](https://github.com/firehol/netdata/wiki/telegram-notifications)
  - [twilio.com](https://github.com/firehol/netdata/wiki/twilio-notifications)
* [[health API calls]]
* [[troubleshooting alarms]]

---

**Netdata Registry**
* [[mynetdata Menu Item]]

---

**Monitoring Info**
* [Monitoring web servers](https://github.com/firehol/netdata/wiki/The-spectacles-of-a-web-server-log-file)
<br/>The spectacles of a web server log file
* [[monitoring ephemeral containers]]<br/>Use netdata to monitor **auto-scaled containers**.
* [[monitoring systemd services]]
* [[monitoring cgroups]]<br/>Use netdata to monitor **containers** and **virtual machines**.
* [[monitoring IPMI]]<br/>Use netdata to monitor **enterprise server hardware**
* [[Monitoring disks]]
* [[Monitoring Go Applications]]

---

**Netdata Badges**
* [[Generating Badges]]

---

**Data Collection**
* **[Add more charts to netdata](https://github.com/firehol/netdata/wiki/Add-more-charts-to-netdata)**
* [[Internal Plugins]]
* [[External Plugins]]
* [[statsd]]

**Binary Modules**
* [[Apps Plugin]]
* [[fping Plugin]]

**Python Modules**
* [[How to write new module]]
* [apache](https://github.com/firehol/netdata/tree/master/python.d#apache)
* [apache_cache](https://github.com/firehol/netdata/tree/master/python.d#apache_cache)
* [bind_rndc](https://github.com/firehol/netdata/tree/master/python.d#bind_rndc)
* [cpuidle](https://github.com/firehol/netdata/tree/master/python.d#cpuidle)
* [cpufreq](https://github.com/firehol/netdata/tree/master/python.d#cpufreq)
* [dns_query_time](https://github.com/firehol/netdata/tree/master/python.d#dns_query_time)
* [dovecot](https://github.com/firehol/netdata/tree/master/python.d#dovecot)
* [elasticsearch](https://github.com/firehol/netdata/tree/master/python.d#elasticsearch)
* [exim](https://github.com/firehol/netdata/tree/master/python.d#exim)
* [fail2ban](https://github.com/firehol/netdata/tree/master/python.d#fail2ban)
* [freeradius](https://github.com/firehol/netdata/tree/master/python.d#freeradius)
* [go_expvar](https://github.com/firehol/netdata/tree/master/python.d#go_expvar)
* [haproxy](https://github.com/firehol/netdata/tree/master/python.d#haproxy)
* [hddtemp](https://github.com/firehol/netdata/tree/master/python.d#hddtemp)
* [ipfs](https://github.com/firehol/netdata/tree/master/python.d#IPFS)
* [isc_dhcpd](https://github.com/firehol/netdata/tree/master/python.d#isc_dhcpd)
* [mdstat](https://github.com/firehol/netdata/tree/master/python.d#mdstat)
* [memcached](https://github.com/firehol/netdata/tree/master/python.d#memcached)
* [mongodb](https://github.com/firehol/netdata/tree/master/python.d#mongodb)
* [mysql](https://github.com/firehol/netdata/tree/master/python.d#mysql)
* [nginx](https://github.com/firehol/netdata/tree/master/python.d#nginx)
* [nsd](https://github.com/firehol/netdata/tree/master/python.d#nsd)
* [ovpn_status_log](https://github.com/firehol/netdata/tree/master/python.d#ovpn_status_log)
* [phpfpm](https://github.com/firehol/netdata/tree/master/python.d#phpfpm)
* [postfix](https://github.com/firehol/netdata/tree/master/python.d#postfix)
* [postgres](https://github.com/firehol/netdata/tree/master/python.d#postgres)
* [rabbitmq](https://github.com/firehol/netdata/tree/master/python.d#rabbitmq)
* [redis](https://github.com/firehol/netdata/tree/master/python.d#redis)
* [retroshare](https://github.com/firehol/netdata/tree/master/python.d#retroshare)
* [sensors](https://github.com/firehol/netdata/tree/master/python.d#sensors)
* [squid](https://github.com/firehol/netdata/tree/master/python.d#squid)
* [smartd_log](https://github.com/firehol/netdata/tree/master/python.d#smartd_log)
* [tomcat](https://github.com/firehol/netdata/tree/master/python.d#tomcat)
* [varnish](https://github.com/firehol/netdata/tree/master/python.d#varnish-cache)
* [web_log](https://github.com/firehol/netdata/tree/master/python.d#web_log)

**Node.js Modules**
* [[General Info - node.d]]
* [named.node.js](https://github.com/firehol/netdata/blob/master/conf.d/node.d/named.conf.md)
* [sma_webbox.node.js](https://github.com/firehol/netdata/blob/master/conf.d/node.d/sma_webbox.conf.md)
* [fronius.node.js](https://github.com/firehol/netdata/blob/master/conf.d/node.d/fronius.conf.md)
* [snmp.node.js](https://github.com/firehol/netdata/blob/master/conf.d/node.d/snmp.conf.md)

**BASH Modules**
* [[General Info - charts.d]]
* [[ap.chart.sh]]
* [[apache.chart.sh]]
* [[cpufreq.chart.sh]]
* [[example.chart.sh]]
* [[mysql.chart.sh]]
* [[nginx.chart.sh]]
* [[nut.chart.sh]]
* [[opensips.chart.sh]]
* [[phpfpm.chart.sh]]
* [[postfix.chart.sh]]
* [[sensors.chart.sh]]
* [[squid.chart.sh]]
* [[tomcat.chart.sh]]

---

**API Documentation**
* [[REST API v1]]
* [[Obsolete REST API pre-v1]]
* [[Receiving netdata metrics from shell scripts]]

---

**Web Dashboards**
* [[Overview]]
* [[Chart Libraries]]
    - [[Dygraph]]
    - [[EasyPieChart]]
    - [[Gauge.js]]
    - [[jQuery Sparkline]]
    - [[Peity]]

* [[Custom Dashboards]]

  Learn how to create dashboards with charts from one or more netdata servers!

---

**Running behind another web server**
* [[Running behind nginx]]
* [[Running behind apache]]
* [[Running behind lighttpd]]
* [[Running behind caddy]]

---

**Donations**
 * [[Donations netdata has received]]

---

**Blog**
* December, 2016

  **[[Linux console tools, fail to report per process CPU usage properly]]**

* April, 2016

  **[[netdata v1.1.0 released]]**

  **[[You should install QoS on all your servers]]**
  (Linux QoS for humans)

  **[[Monitor application bandwidth with Linux QoS]]**
  (Good to do it, anyway)

  **[[Monitoring SYNPROXY]]**
  (Linux TCP Anti-DDoS)


* March, 2016

  **[[Article: Introducing netdata]]**
  (the design principles of netdata)

---

  **[[Other monitoring tools]]**
