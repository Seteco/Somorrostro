
# add more charts to netdata

netdata supports a plugin architecture for data collection. You can add custom plugin following the [External Plugins Guide](https://github.com/firehol/netdata/wiki/External-Plugins).

The following are the currently available plugins:

---

## web servers
### nginx

server|type|netdata<br/>plugin|configuration<br/>file|multiple<br/>servers|notes
:----:|:--:|:----:|:----:|:------------------:|:--------|
nginx|web server real-time metrics|[python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/node.d.plugin)<br/>[nginx.chart.py](https://github.com/firehol/netdata/blob/master/python.d/nginx.chart.py)|[python.d/nginx.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/nginx.conf)|YES|connects to nginx|
apache|web server real-time metrics|[python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/node.d.plugin)<br/>[apache.chart.py](https://github.com/firehol/netdata/blob/master/python.d/apache.chart.py)|[python.d/apache.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/apache.conf)|YES|connects to apache 2.2 and 2.4|

### apache
- apache real-time metrics
- apache logs parsing
### lighttpd
### tomcat

---

## SQL databases
### mysql & mariadb
### postgres

---

## noSQL databases
### redis
### memcached

---

## e-mail servers
### postfix
### exim
### dovecot
### 