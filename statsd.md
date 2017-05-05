statsd is a system to collect data from any application. Applications are sending metrics to it, usually via non-blocking UDP communication, and statsd servers collect these metrics, perform a few simple calculations on them and push them to backend time-series databases.

There is a plethora of client libraries for embedding statsd metrics to any application framework. This makes statsd quite easy to integrate into any application.

## netdata statsd

netdata is a fully featured statsd server. It can collect statsd formatted metrics, visualize them on its dashboards, stream them to other netdata servers or archive them to backend time-series databases.

netdata statsd is inside netdata (an internal plugin, running inside the netdata daemon), it is configured via `netdata.conf` and by-default listens on standard statsd ports (tcp and udp 8125 - yes, netdata statsd server supports both tcp ad udp at the same time).

Since statsd is embedded in netdata, it means you now have a statsd server embedded on all your servers. So, the application can send their metrics on port 8125 on localhost. This provides a distributed statsd implementation, without any single point of failure.

netdata statsd is fast. It can collect more than **1.000.000 metrics per second** on modern hardware, using 1 CPU core (yes, it is single threaded - actually double-threaded, one thread collects metrics, another one updates the charts from the collected data).

## metrics supported by netdata

netdata fully supports the statsd protocol. All statsd client libraries can be used with netdata too.

- **Gauges**

   The application sends `name:value|g`, where `value` is any **decimal/fractional** number, statsd reports the latest value.

   The application may increment or decrement a previous value, by setting the first character of the value to ` + ` or ` - ` (so, the only way to set a gauge to an absolute negative value, is to first set it to zero). 

   Sampling rate is supported (check below).

   When a gauge is not collected and the setting is not to show gaps on the charts (the default), the last value will be shown, until a data collection event changes it.

- **Counters** and **Meters**

   The application sends `name:value|c`, `name:value|C` or `name:value|m`, where `value` is a positive or negative **integer** number of events occurred, statsd reports the rate and the total number of events.

   `:value` can be omitted and statsd will assume it is `1`. `|c`, `|C` and `|m` can be omitted an statsd will assume it is `|m`. So, the application may send just `name` and statsd will parse it as `name:1|m`.

   For counters use `|c` (esty/statsd compatible) or `|C` (brubeck compatible), for meters use `|m`.

   Sampling rate is supported (check below).

   When a counter or meter is not collected and the setting is not to show gaps on the charts (the default), zero will be shown, until a data collection event changes it.

- **Timers** and **Histograms**

   The application sends `name:value|ms`, where ` value` is any decimal/fractional number, statsd reports **min**, **max**, **average**, **average of 95th percentile**, **median** and **standard deviation**.

   For timers use `|ms`, or histograms use `|h`. The only difference between the two, is the `units` of the charts (timers report milliseconds).

   Sampling rate is supported (check below).

   When a timer or histogram is not collected and the setting is not to show gaps on the charts (the default), the last value will be shown, until a data collection event changes it.

- **Sets**

    The application sends `name:value|s`, where `value` is anything (number or text, leading and trailing spaces are removed), statsd reports the number of unique values sent.

   Sampling rate is **not** supported for Sets. `value` is always considered text.

   When a set is not collected and the setting is not to show gaps on the charts (the default), the last value will be shown, until a data collection event changes it.

#### Sampling Rates

The application may append `|@sampling_rate`, where `sampling_rate` is a number from `0.0` to `1.0`, to have statsd extrapolate the value, to predict to total for the whole period. So, if the application reports to statsd a value for 1/10th of the time, it can append `|@0.1` to the metrics it sends to statsd.

#### Overlapping metrics

netdata statsd maintains different indexes for each of the types supported. This means the same metric `name` may exist under different types concurrently.

#### Multiple metrics per packet

netdata accepts multiple metrics per packet if each is terminated with `\n`.

#### TCP packets

netdata listens for both TCP and UDP packets. For TCP though, is it important to always append `\n` on each metric. netdata uses this to detect if a metric is split into multiple TCP packets and will refuse to process it, if the metric is not terminated with `\n`.

#### UDP packets

When sending multiple packets over UDP, it is important not to exceed the network MTU (ususally 1500 bytes minus a few bytes for the headers). 

Be careful when you run multiple statsd instances on the same server. The UDP port will be shared among all of them, so only a part of the metrics will be received by netdata statsd.

## configuration

This is the statsd configuration at `/etc/netdata/netdata.conf`:

```
[statsd]
	# enabled = yes
	# update every (flushInterval) = 1
	# udp messages to process at once = 10
	# create private charts for metrics matching = *
	# max private charts allowed = 200
	# max private charts hard limit = 1000
	# private charts memory mode = save
	# private charts history = 3996
	# histograms and timers percentile (percentThreshold) = 95.00000
	# add dimension for number of events received = yes
	# gaps on gauges (deleteGauges) = no
	# gaps on counters (deleteCounters) = no
	# gaps on meters (deleteMeters) = no
	# gaps on sets (deleteSets) = no
	# gaps on histograms (deleteHistograms) = no
	# gaps on timers (deleteTimers) = no
	# listen backlog = 4096
	# default port = 8125
	# bind to = udp:localhost:8125 tcp:localhost:8125
```

### statsd main config options
- `enabled = yes|no`

   controls if statsd will be enabled for this netdata. The default is enabled.

- `default port = 8125`

   controls the port statsd will use. This is the default, since the next line, allows defining ports too.

- `bind to = udp:localhost tcp:localhost`

   is a space separated list of IPs and ports to listen to. The format is `PROTOCOL:IP:PORT` - if `PORT` is omitted, the `default port` will be used. If `IP` is IPv6, it needs to be enclosed in `[]`. `IP` can also be ` * ` (to listen on all IPs) or a hostname.

- `update every (flushInterval) = 1` seconds, controls the frequency statsd will push the collected metrics to netdata charts.

The rest of the settings are discussed below.

## statsd charts

netdata can visualize statsd collected metrics in 2 ways:

1. Each metric gets its own private chart. This is the default and does not require any configuration (although there are a few options to tweak).

2. Synthetic charts can be created, combining multiple metrics, independently of their metric types. For this type of charts, special configuration is required, to define the chart title, type, units, its dimensions, etc.

### private metric charts

Private charts are controlled with `create private charts for metrics matching = *`. This setting accepts a space separated list of simple patterns (use `*` as wildcard, prepend a pattern with `!` for a negative match, the order of patterns is important).

So to render charts for all `myapp.*` metrics, except `myapp.*.badmetric`, use:

```
create private charts for metrics matching = !myapp.*.badmetric myapp.*
```

The default is to render charts for all metrics.

The `memory mode` of the round robin database and the `history` of private metric charts are controlled with `private charts memory mode` and `private charts history`. The defaults for both settings is to use the global netdata settings. So, you need to edit them only when you want statsd to use different settings compared to the global ones.

If you have thousands of metrics, each with its own chart, you may notice your web browser becomes slow when you view the netdata dashboard (this is a web browser issue we need to address at the netdata UI). So, netdata has a protection to stop creating charts when `max private charts allowed = 200` (soft limit) is reached.

The metrics above this soft limit are still processed by netdata and will be available to be sent to backend time-series databases, up to `max private charts hard limit = 1000`. So, between 200 and 1000 charts, netdata will still generate charts, but they will automatically be created with `memory mode = none` (netdata will not maintain a database for them). These metrics will be sent to backend time series databases, if the backend configuration is set to `as collected`.

Metrics above the hard limit are still collected, but they can only be used in synthetic charts (once a metric is added to chart, it will be sent to backend servers too).

### synthetic statsd charts

> this section needs more details - if you can help, please edit the page and provide more detailed information

Using synthetic charts, you can create dedicated sections on the dashboard to render the charts. You can control everything: the main menu, the submenus, the charts, the dimensions on each chart, etc.

Synthetic charts are organized in **applications**, **charts for each application** and **statsd metrics for each chart**. For each application you need to create a `.conf` file in `/etc/netdata/statsd.d`.

So, to create the statsd application `myapp`, you can create the file `/etc/netdata/statsd.d/myapp.conf`, with this content

```
[app]
	name = myapp
	metrics = myapp.*
	private charts = no
	gaps when not collected = no
	memory mode = ram
	history = 60

[mychart]
	name = mychart
	title = my chart title
	family = my family
	context = chart.context
	units = tests/s
	priority = 91000
	type = area
	dimension = myapp.metric1 m1
	dimension = myapp.metric2 m2
```

`[app]` starts a new application definition. The supported settings in this section are:

- `name` defines the name of the app.
- `metrics` is a netdata simple pattern (space separated patterns, using `*` for wildcard, possibly starting with `!` for negative match). This pattern should match all the possible statsd metrics that will be participating in the application `myapp`.
- `private charts = yes|no`, enables or disables private charts for the metrics matched.
- `gaps when not collected = yes|no`, enables or disables gaps on the charts of the application, when metrics are not collected.
- `memory mode` sets the memory mode for all charts of the application. The default is the global default for netdata (not the global default for statsd private charts).
- `history` sets the size of the round robin database for this application. The default is the global default for netdata (not the global default for statsd private charts).

Then, you can add any number of charts. Each chart should start with `[id]`.  The chart will be called `app name.id`.  `family` controls the submenu on the dashboard. `context` controls the alarm templates. `priority` controls the ordering of the charts on the dashboard. The rest of the settings are informational.

You can add any number of metrics to a chart, using `dimension` lines. These lines accept 4 space separated parameters:

1. the metric name, as it is collected
2. the dimension name, as it should be shown on the chart
3. an optional multipler
4. an optional divider

Using the above configuration `myapp` should get its own section on the dashboard, having one chart with 2 dimensions.

## sending statsd metrics from shell scripts

You can send/update statsd metrics from shell scripts. You can use this feature, to visualize in netdata automated jobs you run on your servers.

The command you need to run is:

```sh
echo "NAME:VALUE|TYPE" | nc -u -w 1 127.0.0.1 8125
```

Where:

- `NAME` is the metric name
- `VALUE` is the value for that metric (**gauges** `|g`, **timers** `|ms` and **histograms** `|h` accept decimal/fractional numbers, **counters** `|c` and **meters** `|m` accept integers, **sets** `|s` accept anything)
- `TYPE` is one of `g`, `ms`, `h`, `c`, `m`, `s` to select the metric type.

So, to set `metric1` as gauge to value `10`, use:

```sh
echo "metric1:10|g" | nc -u -w 1 127.0.0.1 8125
```

To increment `metric2` by `10`, as a counter, use:

```sh
echo "metric2:10|c" | nc -u -w 1 127.0.0.1 8125
```

