# Using netdata with Prometheus

Prometheus is a distributed monitoring system which offers a very simple setup along with a robust data model. Recently netdata added support for Prometheus. I'm going to quickly show you how to install both netdata and prometheus on the same server. We can then use grafana pointed at Prometheus to obtain long term metrics netdata offers. I'm assuming we are starting at a fresh ubuntu shell (whether you'd like to follow along in a VM or a cloud instance is up to you).

## Installing netdata and prometheus

### Installing netdata
There are number of ways to install netdata according to [Installation](https://github.com/firehol/netdata/wiki/Installation)  
The suggested way of installing the latest netdata and keep it upgrade automatically. Using one line installation:

```
bash <(curl -Ss https://my-netdata.io/kickstart.sh)
```
At this point we should have netdata listening on port 19999. Attempt to take your browser here:

```
http://your.netdata.ip:19999
```

*(replace `your.netdata.ip` with the IP or hostname of the server running netdata)*

### Installing Prometheus
In order to install prometheus we are going to introduce our own systemd startup script along with an example of prometheus.yaml configuration. Prometheus needs to be pointed to your server at a specific target url for it to scrape netdata's api. Prometheus is always a pull model meaning netdata is the passive client within this architecture. Prometheus always initiates the connection with netdata.

##### Download Prometheus

```sh
wget -O /tmp/prometheus-1.7.1.linux-amd64.tar.gz https://github.com/prometheus/prometheus/releases/download/v1.7.1/prometheus-1.7.1.linux-amd64.tar.gz 
```

##### Create prometheus system user

```sh
sudo useradd -r prometheus
```

#### Create prometheus directory

```sh
sudo mkdir /opt/prometheus
chown prometheus:prometheus /opt/prometheus
```

#### Untar prometheus directory

```sh
tar -xvf /tmp/prometheus-1.7.1.linux-amd64.tar.gz -C /opt/prometheus --strip=1
```

#### Install prometheus.yml

We will use the following `prometheus.yml` file. Save it at `/opt/prometheus/prometheus.yml`.

Make sure to replace `your.netdata.ip` with the IP or hostname of the host running netdata. 

```yaml
# my global config
global:
  scrape_interval:     5s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 5s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

  # Attach these labels to any time series or alerts when communicating with
  # external systems (federation, remote storage, Alertmanager).
  external_labels:
      monitor: 'codelab-monitor'

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first.rules"
  # - "second.rules"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'prometheus'

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ['0.0.0.0:9090']

  - job_name: 'netdata-scrape'

    metrics_path: '/api/v1/allmetrics'
    params:
      # format: prometheus | prometheus_all_hosts
      format: [prometheus]
      # source: as-collected | raw | average | sum | volume
      #source: [as-collected]
      # server name for this prometheus - the default is the client IP
      #server: prometheus1
    honor_labels: true

    static_configs:
      - targets: ['{your.netdata.ip}:19999']
```

#### Install prometheus.service

Save this service file as `/etc/systemd/system/prometheus.service`:

```
[Unit]
Description=Prometheus Server
AssertPathExists=/opt/prometheus

[Service]
Type=simple
WorkingDirectory=/opt/prometheus
User=prometheus
Group=prometheus
ExecStart=/opt/prometheus/prometheus -config.file=/opt/prometheus/prometheus.yml -log.level=info
ExecReload=/bin/kill -SIGHUP $MAINPID
ExecStop=/bin/kill -SIGINT $MAINPID

[Install]
WantedBy=multi-user.target
```

##### Start Prometheus

```
sudo systemctl start prometheus
sudo systemctl enable prometheus
```

Prometheus should now start and listen on port 9090. Attempt to head there with your browser. 

If everything is working correctly when you fetch `http://your.prometheus.ip:9090` you will see a 'Status' tab. Click this and click on 'targets' We should see the netdata host as a scraped target. 

---

## netdata support for prometheus

> IMPORTANT: the format netdata sends metrics to prometheus has changed since netdata v1.6. The new format allows easier queries for metrics and supports both `as collected` and normalized metrics.

Before explaining the changes, we have to understand the key differences between netdata and prometheus.

### understanding netdata metrics

##### charts

Each chart in netdata has several properties (common to all its metrics):

- `chart_id` - uniquely identifies a chart.

- `chart_name` - a more human friendly name for `chart_id`, also unique.

- `context` - this is the template of the chart. All disk I/O charts have the same context, all mysql requests charts have the same context, etc. This is used for alarm templates to match all the charts they should be attached to.

- `family` groups a set of charts together. It is used as the submenu of the dashboard.

- `units` is the units for all the metrics attached to the chart.

##### dimensions

Then each netdata chart contains metrics called `dimensions`. All the dimensions of a chart have the same units of measurement, and are contextually in the same category (ie. the metrics for disk bandwidth are `read` and `write` and they are both in the same chart).

### netdata data source

netdata can send metrics to prometheus from 3 data sources:

- `as collected` or `raw` - this data source sends the metrics to prometheus as they are collected. No conversion is done by netdata. The latest value for each metric is just given to prometheus. This is the most preferred method by prometheus, but it is also the harder to work with. To work with this data source, you will need to understand how to get meaningful values out of them.

   The format of the metrics is: `CONTEXT_DIMENSION{chart="CHART",family="FAMILY"}`.

- `average` - this data source uses the netdata database to send the metrics to prometheus as they are presented on the netdata dashboard. So, all the metrics are sent as gauges, at the units they are presented in the netdata dashboard charts. This is the easiest to work with.

   The format of the metrics is: `CONTEXT{chart="CHART",family="FAMILY",dimension="DIMENSION"}`.

   When this source is used, netdata keeps track of the last access time for each prometheus server fetching the metrics. This last access time is used at the subsequent queries of the same prometheus server to identify the time-frame the `average` will be calculated. So, no matter how frequently prometheus scrapes netdata, it will get all the database data. To identify each prometheus server, netdata uses by default the IP of the client fetching the metrics. If there are multiple prometheus servers fetching data from the same netdata, using the same IP, each prometheus server can append `server=NAME` to the URL. Netdata will use this `NAME` to uniquely identify the prometheus server.

- `sum` or `volume`, is like `average` but instead of averaging the values, it sums them.

   The format of the metrics and all the other operations are the same with `average`. 

Keep in mind that early versions of netdata were sending the metrics as: `CHART_DIMENSION{}`.


### Querying Metrics

Fetch with your web browser this URL:

`http://your.netdata.ip:19999/api/v1/allmetrics?format=prometheus&help=yes`

*(replace `your.netdata.ip` with the ip or hostname of your netdata server)*

netdata will respond with all the metrics it sends to prometheus.

If you search that page for `"system.cpu"` you will find all the metrics netdata is exporting to prometheus for this chart.  `system.cpu` is the chart name on the netdata dashboard (on the netdata dashboard all charts have a text heading such as : `Total CPU utilization (system.cpu)`. What we are interested here in the chart name: `system.cpu`).

Searching for `"system.cpu"` reveals:

```sh
# COMMENT HELP netdata_system_cpu netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "guest_nice", value gives percentage (gauge)
netdata_system_cpu{chart="system.cpu",family="cpu",dimension="guest_nice"} 0.0000000 1499716006030
# COMMENT HELP netdata_system_cpu netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "guest", value gives percentage (gauge)
netdata_system_cpu{chart="system.cpu",family="cpu",dimension="guest"} 0.0000000 1499716006030
# COMMENT HELP netdata_system_cpu netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "steal", value gives percentage (gauge)
netdata_system_cpu{chart="system.cpu",family="cpu",dimension="steal"} 0.0000000 1499716006030
# COMMENT HELP netdata_system_cpu netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "softirq", value gives percentage (gauge)
netdata_system_cpu{chart="system.cpu",family="cpu",dimension="softirq"} 2.1164020 1499716006030
# COMMENT HELP netdata_system_cpu netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "irq", value gives percentage (gauge)
netdata_system_cpu{chart="system.cpu",family="cpu",dimension="irq"} 0.0000000 1499716006030
# COMMENT HELP netdata_system_cpu netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "user", value gives percentage (gauge)
netdata_system_cpu{chart="system.cpu",family="cpu",dimension="user"} 1.3227513 1499716006030
# COMMENT HELP netdata_system_cpu netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "system", value gives percentage (gauge)
netdata_system_cpu{chart="system.cpu",family="cpu",dimension="system"} 3.1746030 1499716006030
# COMMENT HELP netdata_system_cpu netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "nice", value gives percentage (gauge)
netdata_system_cpu{chart="system.cpu",family="cpu",dimension="nice"} 1.5873016 1499716006030
# COMMENT HELP netdata_system_cpu netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "iowait", value gives percentage (gauge)
netdata_system_cpu{chart="system.cpu",family="cpu",dimension="iowait"} 2.9100530 1499716006030
# COMMENT HELP netdata_system_cpu netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "idle", value gives percentage (gauge)
netdata_system_cpu{chart="system.cpu",family="cpu",dimension="idle"} 88.8888900 1499716006030
```
*(netdata response for `system.cpu` with source=`average`)*

In `average` or `sum` data sources, all values are normalized and are reported to prometheus as gauges. Now, use the 'expression' text form in prometheus. Begin to type the metrics we are looking for: `netdata_system_cpu`. You should see that the text form begins to auto-fill as prometheus knows about this metric.

If the data source was `as collected`, the response would be:

```sh
# COMMENT HELP netdata_system_cpu_guest_nice netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "guest_nice", value * 1 / 1 delta gives percentage (counter)
netdata_system_cpu_guest_nice{chart="system.cpu",family="cpu"} 0 1499716718029
# COMMENT HELP netdata_system_cpu_guest netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "guest", value * 1 / 1 delta gives percentage (counter)
netdata_system_cpu_guest{chart="system.cpu",family="cpu"} 0 1499716718029
# COMMENT HELP netdata_system_cpu_steal netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "steal", value * 1 / 1 delta gives percentage (counter)
netdata_system_cpu_steal{chart="system.cpu",family="cpu"} 0 1499716718029
# COMMENT HELP netdata_system_cpu_softirq netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "softirq", value * 1 / 1 delta gives percentage (counter)
netdata_system_cpu_softirq{chart="system.cpu",family="cpu"} 3664155 1499716718029
# COMMENT HELP netdata_system_cpu_irq netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "irq", value * 1 / 1 delta gives percentage (counter)
netdata_system_cpu_irq{chart="system.cpu",family="cpu"} 0 1499716718029
# COMMENT HELP netdata_system_cpu_user netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "user", value * 1 / 1 delta gives percentage (counter)
netdata_system_cpu_user{chart="system.cpu",family="cpu"} 29025829 1499716718029
# COMMENT HELP netdata_system_cpu_system netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "system", value * 1 / 1 delta gives percentage (counter)
netdata_system_cpu_system{chart="system.cpu",family="cpu"} 18994364 1499716718029
# COMMENT HELP netdata_system_cpu_nice netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "nice", value * 1 / 1 delta gives percentage (counter)
netdata_system_cpu_nice{chart="system.cpu",family="cpu"} 11354309 1499716718029
# COMMENT HELP netdata_system_cpu_iowait netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "iowait", value * 1 / 1 delta gives percentage (counter)
netdata_system_cpu_iowait{chart="system.cpu",family="cpu"} 7485331 1499716718029
# COMMENT HELP netdata_system_cpu_idle netdata chart "system.cpu", context "system.cpu", family "cpu", dimension "idle", value * 1 / 1 delta gives percentage (counter)
netdata_system_cpu_idle{chart="system.cpu",family="cpu"} 593674112 1499716718029
```
*(netdata response for `system.cpu` with source=`as-collected`)*

In this case, there are many different metrics. netdata sends them individually because each metric may have a different algorithm, multiplier and divider. Since we are dealing with counters we need to analyze these metrics over a time period. We do this with the 'irate()' function. Type this into the expression bar:

```
irate(netdata_system_cpu_user[2m])
```

You now have a metric which searches prometheus's history up to two minutes to find the percentage over time. 

For more information check prometheus documentation.

### Streaming data from upstream hosts

The `format=prometheus` parameter only exports the host's netdata metrics.  If you are using the master/slave functionality of netdata this ignores any upstream hosts - so you should consider using the below in your **prometheus.yml**:

```
    metrics_path: '/api/v1/allmetrics'
    params:
      format: [prometheus_all_hosts]
    honor_labels: true
```

This will report all upstream host data, and `honor_labels` will make Prometheus take note of the instance names provided.

### TYPE and HELP

To save bandwidth, and because prometheus does not use them anyway, `# TYPE` and `# HELP` lines are suppressed. If wanted they can be reenabled via `types=yes` and `help=yes`, e.g. `/api/v1/allmetrics?format=prometheus&types=yes&help=yes`

### Names and IDs

netdata supports names and IDs for charts and dimensions. Usually IDs are unique identifiers as read by the system and names are human friendly labels (also unique).

Most charts and metrics have the same ID and name, but in several cases they are different: disks with device-mapper, interrupts, QoS classes, statsd synthetic charts, etc.

The default is controlled in `netdata.conf`:

```
[backend]
	send names instead of ids = yes | no
```

You can overwrite it from prometheus, by appending to the URL:

* `&names=no` to get IDs (the old behaviour)
* `&names=yes` to get names

### Filtering metrics sent to prometheus

netdata can filter the metrics it sends to prometheus with this setting:

```
[backend]
	send charts matching = *
```

This settings accepts a space separated list of patterns to match the **charts** to be sent to prometheus. Each pattern can use ` * ` as wildcard, any number of times (e.g `*a*b*c*` is valid). Patterns starting with ` ! ` give a negative match (e.g `!*.bad users.* groups.*` will send all the users and groups except `bad` user and `bad` group). The order is important: the first match (positive or negative) left to right, is used.

### Changing the prefix of netdata metrics

netdata sends all metrics prefixed with `netdata_`. You can change this in `netdata.conf`, like this:

```
[backend]
	prefix = netdata
```

It can also be changed from the URL, by appending `&prefix=netdata`.

### accuracy of `average` and `sum` data sources

When the data source is set to `average` or `sum`, netdata remembers the last access of each client accessing prometheus metrics and uses this last access time to respond with the `average` or `sum` of all the entries in the database since that. This means that prometheus servers are not loosing data when they access netdata with data source = `average` or `sum`.

To uniquely identify each prometheus server, netdata uses the IP of the client accessing the metrics. If however the IP is not good enough for identifying a single prometheus server (e.g. when prometheus servers are accessing netdata through a web proxy, or when multiple prometheus servers are NATed to a single IP), each prometheus may append `&server=NAME` to the URL. This `NAME` is used by netdata to uniquely identify each prometheus server and keep track of its last access time.
