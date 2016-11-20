netdata supports backends for archiving the metrics, or providing long term dashboards, using grafana or other tools.

Since netdata collects thousands of metrics per server per second, which would easily congest any backend server when several netdata servers are sending data to it, netdata allows sending metrics at a lower frequency. So, although netdata collects metrics every second, it can send to the backend servers averages or sums every X seconds (although it can send them per second if you need it to).

## features

1. Supported backends

   1. **graphite** (plaintext interface)

      metrics are sent to graphite as `prefix.hostname.chart.dimension`. `prefix` is configured below, `hostname` is the hostname of the machine.

   2. **opentsdb** (telnet interface)

      metrics are sent to opentsdb as `prefix.chart.dimension` with tag `host=hostname`.

2. Only one backed may be active at a time.

3. All metrics are transferred to the backend - netdata does not implement any metric filtering.

4. Three modes of operation (for all backends):

   1. `as collected`: the latest collected value is sent to the backend. This means that if netdata is configured to send data to the backend every 10 seconds, only 1 out of 10 values will appear at the backend server. The values are sent exactly as collected, before any multipliers or dividers applied and before any interpolation. This mode emulates other data collectors, such as `collectd`.

   2. `average`: the average of the interpolated values shown on the netdata graphs is sent to the backend. So, if netdata is configured to send data to the backend server every 10 seconds, the average of the 10 values shown on the netdata charts will be used.

   3. `sum` or `volume`: the sum of the interpolated values shown on the netdata graphs is sent to the backend. So, if netdata is configured to send data to the backend every 10 seconds, the sum of the 10 values shown on the netdata charts will be used.

5. This code is smart enough, not to slow down netdata, independently of the speed of the backend server.

## configuration

A new section will appear in netdata.conf, like this:

```
[backend]
	# enabled = no
	# type = graphite
	# destination = localhost
	# data source = average
	# prefix = netdata
	# hostname = my-name
	# update every = 10
	# buffer on failures = 10
	# timeout ms = 20000
```

- `enabled = yes/no`, enables or disables sending data to a backend

- `type = graphite` or `type = opentsdb`, selects the backend type

- `destination = host`, accepts a space separated list of hostnames, IPs (IPv4 and IPv6) and ports to connect to. Netdata will use the first available to send the metrics. The format is like this: `host1:port1 host2:port2 host3:port3 ...`. For IPv6 addresses this is supported: `[IPV6_IP]:PORT` (like the web clients).

- `data source = as collected`, or `data source = average`, or `data source = sum`, selects the kind of data that will be sent to the backend.

- `hostname = my-name`, is the hostname to be used for sending data to the backend server. By default this is `[global].hostname`.

- `prefix = netdata`, is the prefix to add to all metrics.

- `update every = 10`, is the number of seconds between sending data to the backend. netdata will add some randomness to this number, to prevent stressing the backend server when many netdata servers send data to the same backend. This randomness does not affect the quality of the data, only the time they are sent.

- `buffer on failures = 10`, is the number of iterations (each iteration is `[backend].update every` seconds) to buffer data, when the backend is not available. If the backend fails to receive the data after that many failures, data loss on the backend is expected (netdata will also log it).

- `timeout ms = 20000`, is the timeout in milliseconds to wait for the backend server to process the data. By default this is `2 * update_every * 1000`.

## monitoring operation

netdata provides 5 charts:

1. **Buffered metrics**, the number of metrics netdata added to the buffer for dispatching them to the backend server.
2. **Buffered data size**, the amount of data (in KB) netdata added the buffer.
3. **Backend latency**, the time the backend server needed to process the data netdata sent. If there was a re-connection involved, this includes the connection time.
4. **Backend operations**, the number of operations performed by netdata.
5. **Backend thread CPU usage**, the CPU resources consumed by the netdata thread, that is responsible for sending the metrics to the backend server.

![image](https://cloud.githubusercontent.com/assets/2662304/20463536/eb196084-af3d-11e6-8ee5-ddbd3b4d8449.png)

## alarms

The latest version of the alarms configuration for monitoring the backend is here: https://github.com/firehol/netdata/blob/master/conf.d/health.d/backend.conf

netdata adds 4 alarms:

1. `backend_last_buffering`, number of seconds since the last successful buffering of backend data
2. `backend_metrics_sent`, percentage of metrics sent to the backend server
3. `backend_metrics_lost`, number of metrics lost due to repeating failures to contact the backend server
4. `backend_slow`, the percentage of time between iterations needed by the backend time to process the data sent by netdata

![image](https://cloud.githubusercontent.com/assets/2662304/20463779/a46ed1c2-af43-11e6-91a5-07ca4533cac3.png)

