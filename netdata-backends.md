
> New to netdata? Check its demo: **[http://my-netdata.io/](http://my-netdata.io/)**
>
> [![User Base](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=persons&label=user%20base&units=null&value_color=blue&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry) [![Monitored Servers](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=machines&label=servers%20monitored&units=null&value_color=orange&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry) [![Sessions Served](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_sessions&label=sessions%20served&units=null&value_color=yellowgreen&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry)
> 
> [![New Users Today](http://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=persons&after=-86400&options=unaligned&group=incremental-sum&label=new%20users%20today&units=null&value_color=blue&precision=0&v40)](https://registry.my-netdata.io/#netdata_registry) [![New Machines Today](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=machines&group=incremental-sum&after=-86400&options=unaligned&label=servers%20added%20today&units=null&value_color=orange&precision=0&v40)](https://registry.my-netdata.io/#netdata_registry) [![Sessions Today](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_sessions&after=-86400&group=incremental-sum&options=unaligned&label=sessions%20served%20today&units=null&value_color=yellowgreen&precision=0&v40)](https://registry.my-netdata.io/#netdata_registry)

---

netdata supports backends for archiving the metrics, or providing long term dashboards, using grafana or other tools, like this:

![image](https://cloud.githubusercontent.com/assets/2662304/20649711/29f182ba-b4ce-11e6-97c8-ab2c0ab59833.png)

Since netdata collects thousands of metrics per server per second, which would easily congest any backend server when several netdata servers are sending data to it, netdata allows sending metrics at a lower frequency. So, although netdata collects metrics every second, it can send to the backend servers averages or sums every X seconds (though, it can send them per second if you need it to).

## features

1. Supported backends

   1. **graphite** (`plaintext interface`, used by **Graphite**, **InfluxDB**, **KairosDB**, **Blueflood**, **ElasticSearch** via logstash tcp input and the graphite codec, etc)

      metrics are sent to the backend server as `prefix.hostname.chart.dimension`. `prefix` is configured below, `hostname` is the hostname of the machine (can also be configured).

   2. **opentsdb** (`telnet interface`, used by **OpenTSDB**, **InfluxDB**, **KairosDB**, etc)

      metrics are sent to opentsdb as `prefix.chart.dimension` with tag `host=hostname`.

   3. **json** document DBs

      metrics are sent to a document db, `JSON` formatted.

2. Only one backend may be active at a time.

3. All metrics are transferred to the backend - netdata does not implement any metric filtering.

4. Three modes of operation (for all backends):

   1. `as collected`: the latest collected value is sent to the backend. This means that if netdata is configured to send data to the backend every 10 seconds, only 1 out of 10 values will appear at the backend server. The values are sent exactly as collected, before any multipliers or dividers applied and before any interpolation. This mode emulates other data collectors, such as `collectd`.

   2. `average`: the average of the interpolated values shown on the netdata graphs is sent to the backend. So, if netdata is configured to send data to the backend server every 10 seconds, the average of the 10 values shown on the netdata charts will be used. **If you can't decide which mode to use, use `average`.**

   3. `sum` or `volume`: the sum of the interpolated values shown on the netdata graphs is sent to the backend. So, if netdata is configured to send data to the backend every 10 seconds, the sum of the 10 values shown on the netdata charts will be used.

5. This code is smart enough, not to slow down netdata, independently of the speed of the backend server.

## configuration

In `/etc/netdata/netdata.conf` you should have something like this (if not download the latest version of `netdata.conf` from your netdata):

```
[backend]
	enabled = yes | no
	type = graphite | opentsdb | json
        opentsdb host tags = space separated list of TAG=VALUE
	destination = space separated list of [PROTOCOL:]HOST[:PORT] - the first working will be used
	data source = average | sum | as collected
	prefix = netdata
	hostname = my-name
	update every = 10
	buffer on failures = 10
	timeout ms = 20000
```

- `enabled = yes/no`, enables or disables sending data to a backend

- `type = graphite` or `type = opentsdb` or `type = json`, selects the backend type

- `destination = host`, accepts **a space separated list** of hostnames, IPs (IPv4 and IPv6) and ports to connect to. Netdata will use the first available to send the metrics.

   The format of each item in this list, is: `[PROTOCOL:]IP[:PORT]`.

   `PROTOCOL` can be `udp` or `tcp`. `tcp` is the default and only supported by the current backends.

   `IP` can be `XX.XX.XX.XX` (IPv4), or `[XX:XX...XX:XX]` (IPv6). For IPv6 you can to enclose the IP in `[]` to separate it from the port.

   `PORT` can be a number of a service name. If omitted, the default port for the backend will be used (graphite = 2003, opentstb = 4242).

   Example IPv4:

```
   destination = 10.11.14.2:4242 10.11.14.3:4242 10.11.14.4:4242
```

   Example IPv6 and IPv4 together:
   
```
   destination = [ffff:...:0001]:2003 10.11.12.1:2003
```

   When multiple servers are defined, netdata will try the next one when the first one fails. This allows you to load-balance different servers: give your backend servers in different order on each netdata.

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
3. ~~**Backend latency**, the time the backend server needed to process the data netdata sent. If there was a re-connection involved, this includes the connection time.~~ (this chart has been removed, because it only measures the time netdata needs to give the data to the O/S - since the backend servers do not ack the reception, netdata does not have any means to measure this properly)
4. **Backend operations**, the number of operations performed by netdata.
5. **Backend thread CPU usage**, the CPU resources consumed by the netdata thread, that is responsible for sending the metrics to the backend server.

![image](https://cloud.githubusercontent.com/assets/2662304/20463536/eb196084-af3d-11e6-8ee5-ddbd3b4d8449.png)

## alarms

The latest version of the alarms configuration for monitoring the backend is here: https://github.com/firehol/netdata/blob/master/conf.d/health.d/backend.conf

netdata adds 4 alarms:

1. `backend_last_buffering`, number of seconds since the last successful buffering of backend data
2. `backend_metrics_sent`, percentage of metrics sent to the backend server
3. `backend_metrics_lost`, number of metrics lost due to repeating failures to contact the backend server
4. ~~`backend_slow`, the percentage of time between iterations needed by the backend time to process the data sent by netdata~~ (this was misleading and has been removed).

![image](https://cloud.githubusercontent.com/assets/2662304/20463779/a46ed1c2-af43-11e6-91a5-07ca4533cac3.png)

## adding more backends

netdata already has the code to connect to a TCP socket and send data: https://github.com/firehol/netdata/blob/master/src/backends.c

To add new backends you will have to provide:

1. Functions to construct the part of the message for each metric. This is an example for `opentsdb`: https://github.com/firehol/netdata/blob/46785b4cc1a3d5f7264e8e73dbe4dd16a246b9f6/src/backends.c#L78-L95

2. A function to process the response from the backend (any any). This is an example for `opentsdb`: https://github.com/firehol/netdata/blob/46785b4cc1a3d5f7264e8e73dbe4dd16a246b9f6/src/backends.c#L114-L129

3 Add the new backend at the configuration selection. This is an example for `opentsdb`: https://github.com/firehol/netdata/blob/46785b4cc1a3d5f7264e8e73dbe4dd16a246b9f6/src/backends.c#L195-L203

By providing such code, netdata can send its metrics to any other TCP-based backend.

Keep in mind that the existing code is uni-directional. netdata only checks that the backend server received the data stream with the metrics. It does not have any means the verify the backend server did something with them.

## InfluxDB setup as netdata backend (example)
You can find blog post with example: how to use InfluxDB with netdata [here](https://blog.hda.me/2017/01/09/using-netdata-with-influxdb-backend.html)  
Also you can reuse grafana dashboard setups for netdata and for lxd containers on netdata host:  
[Netdata](https://grafana.net/dashboards/1295)  
[lxd container](https://grafana.net/dashboards/1291)  
InfluxDB backend used in dashboards above, but you can easily edit json files for another backend, and also you can reuse lxd container dashboard for lxc containers.