
# add more charts to netdata

netdata supports a plugin architecture for data collection. You can add custom plugin following the [External Plugins Guide](https://github.com/firehol/netdata/wiki/External-Plugins).

The following are the currently available plugins:

---

## web servers
### nginx

server|netdata<br/>plugin|plugin<br/>module|configuration<br/>file|multiple<br/>servers|operation
:----:|:----:|:----:|:----:|:------------------:|:--------|
nginx|[python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/node.d.plugin)|[nginx.chart.py](https://github.com/firehol/netdata/blob/master/python.d/nginx.chart.py)|[python.d/nginx.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/nginx.conf)|YES|connects to nginx|

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