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
    - [[Memory Deduplication - Kernel Same Page Merging - KSM]]
    - [[netdata virtual memory size]]

---

**Health monitoring - alarms**
<br/>alarms and alarm notifications in netdata

* [[health-monitoring]]
* [health configuration reference](https://github.com/firehol/netdata/wiki/health-configuration-reference)
* [[alarm notifications]]
  - [HTML5 web notifications](https://github.com/firehol/netdata/wiki/web-browser-notifications)
  - [emails](https://github.com/firehol/netdata/wiki/email-notifications)
  - [discord.com](https://github.com/firehol/netdata/wiki/discord-notifications)
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

**Backends**
<br/>archiving netdata collected metrics to a time-series database

* [[netdata-backends]]<br/>`graphite`, `opentsdb`, `kairosdb`, `influxdb`
* [netdata with prometheus](https://github.com/firehol/netdata/wiki/Using-Netdata-with-Prometheus)

---

**Netdata Registry**
* [[mynetdata Menu Item]]

---

**Monitoring Info**
* [[Monitoring disks]]

---

**Netdata Badges**
* [[Generating Badges]]

---

**Data Collection**
* [[Internal Plugins]]
* [[External Plugins]]

**Binary Modules**
* [[Apps Plugin]]
* [[fping Plugin]]

**Python Modules**
* [[How to write new module]]
* [apache](https://github.com/firehol/netdata/tree/master/python.d#apache)
* [apache_cache](https://github.com/firehol/netdata/tree/master/python.d#apache_cache)
* [bind_rndc](https://github.com/firehol/netdata/tree/master/python.d#bind_rndc)
* [cpufreq](https://github.com/firehol/netdata/tree/master/python.d#cpufreq)
* [dovecot](https://github.com/firehol/netdata/tree/master/python.d#dovecot)
* [elasticsearch](https://github.com/firehol/netdata/tree/master/python.d#elasticsearch)
* [exim](https://github.com/firehol/netdata/tree/master/python.d#exim)
* [fail2ban](https://github.com/firehol/netdata/tree/master/python.d#fail2ban)
* [freeradius](https://github.com/firehol/netdata/tree/master/python.d#freeradius)
* [haproxy](https://github.com/firehol/netdata/tree/master/python.d#haproxy)
* [hddtemp](https://github.com/firehol/netdata/tree/master/python.d#hddtemp)
* [ipfs](https://github.com/firehol/netdata/tree/master/python.d#IPFS)
* [isc_dhcpd](https://github.com/firehol/netdata/tree/master/python.d#isc_dhcpd)
* [mdstat](https://github.com/firehol/netdata/tree/master/python.d#mdstat)
* [memcached](https://github.com/firehol/netdata/tree/master/python.d#memcached)
* [mysql](https://github.com/firehol/netdata/tree/master/python.d#mysql)
* [nginx](https://github.com/firehol/netdata/tree/master/python.d#nginx)
* [nginx_log](https://github.com/firehol/netdata/tree/master/python.d#nginx_log)
* [ovpn_status_log](https://github.com/firehol/netdata/tree/master/python.d#ovpn_status_log)
* [phpfpm](https://github.com/firehol/netdata/tree/master/python.d#phpfpm)
* [postfix](https://github.com/firehol/netdata/tree/master/python.d#postfix)
* [redis](https://github.com/firehol/netdata/tree/master/python.d#redis)
* [sensors](https://github.com/firehol/netdata/tree/master/python.d#sensors)
* [squid](https://github.com/firehol/netdata/tree/master/python.d#squid)
* [tomcat](https://github.com/firehol/netdata/tree/master/python.d#tomcat)
* [varnish](https://github.com/firehol/netdata/tree/master/python.d#varnish-cache)

**Node.js Modules**
* [[General Info - node.d]]
* [named.node.js](https://github.com/firehol/netdata/blob/master/conf.d/node.d/named.conf.md)
* [sma_webbox.node.js](https://github.com/firehol/netdata/blob/master/conf.d/node.d/sma_webbox.conf.md)
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


**Advanced configurations**
* [[netdata for IoT]]
  (configure netdata to lower its resources)
* [[high performance netdata]]
  (running netdata public to the internet)

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
