
# add more charts to netdata

netdata supports a plugin architecture for data collection. You can add custom plugins following the [External Plugins Guide](https://github.com/firehol/netdata/wiki/External-Plugins).

The following are the currently available plugins:

---

server|type|netdata<br/>plugin|configuration<br/>file|multiple<br/>servers|notes
:----:|:--:|:----:|:----:|:------------------:|:--------|
apache|web server|[python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/node.d.plugin)<br/>[apache.chart.py](https://github.com/firehol/netdata/blob/master/python.d/apache.chart.py)|[python.d/apache.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/apache.conf)|YES|apache 2.2 and 2.4|
nginx|web server|[python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/node.d.plugin)<br/>[nginx.chart.py](https://github.com/firehol/netdata/blob/master/python.d/nginx.chart.py)|[python.d/nginx.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/nginx.conf)|YES||
tomcat|web server|[python.d.plugin](https://github.com/firehol/netdata/blob/master/plugins.d/node.d.plugin)<br/>[tomcat.chart.py](https://github.com/firehol/netdata/blob/master/python.d/tomcat.chart.py)|[python.d/tomcat.conf](https://github.com/firehol/netdata/blob/master/conf.d/python.d/tomcat.conf)|YES||
