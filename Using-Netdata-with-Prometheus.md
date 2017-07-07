# Using netdata with Prometheus

Prometheus is a distributed monitoring system which offers a very simple setup along with a robust data model. Recently netdata added support for Prometheus. I'm going to quickly show you how to install both netdata and prometheus on the same server. We can then use grafana pointed at Prometheus to obtain long term metrics netdata offers. I'm assuming we are starting at a fresh ubuntu shell (whether you'd like to follow along in a VM or a cloud instance is up to you).

### Installing netdata
There are number of ways to install netdata according to [Installation ](https://github.com/firehol/netdata/wiki/Installation)  
The suggested way of installing the latest netdata and keep it upgrade automatically. Using one line intallation.
```
bash <(curl -Ss https://my-netdata.io/kickstart.sh)
```
At this point we should have netdata listning on port 19999. Attempt to take your browser here 

```
http://{ip_of_vm}:19999
```

### Installing Prometheus
In order to install prometheus we are going to introduce our own systemd startup script along with an example of prometheus.yaml configuration. Prometheus needs to be pointed to your server at a specific target url for it to scrape netdata's api. Prometheus is always a pull model meaning netdata is the passive client within this architecture. Prometheus always initiates the connection with netdata.

##### Download Prometheus

```
wget -O /tmp/prometheus-1.4.1.linux-amd64.tar.gz https://github.com/prometheus/prometheus/releases/download/v1.4.1/prometheus-1.4.1.linux-amd64.tar.gz 
```

##### Create prometheus user
```
sudo useradd prometheus
```

#### Create prometheus directory
```
sudo mkdir /opt/prometheus
chown prometheus:prometheus /opt/prometheus
```

#### Untar prometheus directory

```
tar -xvf /tmp/prometheus-1.4.1.linux-amd64.tar.gz -C /opt/prometheus --strip=1
```

#### Install prometheus.yml
We will use the following prometheus.yml file, place this into /opt/prometheus/prometheus.yml. Make sure to replace '{ip_of_vm}' with your VM or cloud instance's ip (in this case localhost will work fine). 

``` yaml
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
      format: [prometheus]

    static_configs:
      - targets: ['{ip_of_vm}:19999']
```

#### Install prometheus service

Install this service file into /etc/systemd/system/prometheus.service

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

We should now have prometheus listening on port 9090. Attempt to head there in your browser. 

If everything is working correctly when you resolve the page at :9090 you will see a 'Status' tab. Click this and click on 'targets' We should see the netdata host as a scraped target. 

### Querying Metrics

We will focus on two items for making querying data points easy. If we go to this url (replacing {ip_of_vm} with the ip or hostname of your vm) we see a large test output of all the metrics netdata is exporting. 
http://{ip_of_vm}:19999/api/v1/allmetrics?format=prometheus

If you go here:
http://{ip_of_vm}:19999/ 
We get to netdata's main gui. 

If we explore netdata's gui you will notice that almost all of the charts have a text heading such as : Total CPU utilization (system.cpu)

What we are interested here in the metrics name system.cpu. This metrics name corresponds with the prometheus metric listed in the first url. If we search that page for 'system_cpu' You will obtain the same metrics that netdata gui is exporting. 

So for example let's say we would like to query the metrics for system.cpu.user. We would held to the prometheus metrics at the link and find 'system_cpu_user'and we find:

\# TYPE system_cpu_user counter

system_cpu_user{instance="netdata_test"} 198838 1484610710070

What we have here is a counter and we can now use this metrics within prometheus. Let's go to prometheus's gui at http://{ip_of_vm}:9090. You should be within the 'expression' text form. Begin to type the metrics we are looking for: system_cpu_user. You should see that the text form begins to auto-fill as prometheus knows about this metric. Since we are dealing with a counter we need to analyze this metrics over a time period. We do this with the 'irate()' function. Type this into the expression bar:

```
irate(system_cpu_user[2m])
```

You now have a metric which searches prometheus's history up to two minutes to find the percentage over time. 

This tutorial is really just to get your going. If you're interested I would highly recommend reading the Prometheus's documentation along with netdata's.

### Streaming data from upstream hosts

The `format=prometheus` parameter only exports the host's netdata metrics.  If you are using the master/slave
functionality of netdata this ignores any upstream hosts - so you should consider using the below in your **prometheus.yml**:

```
    metrics_path: '/api/v1/allmetrics'
    params:
      format: [prometheus_all_hosts]
    honor_labels: true
```
This will report all upstream host data, and `honor_labels` will make Prometheus take note of the instance names provided.  `format=prometheus_all_hosts` also suppresses all **TYPE** and **HELP** lines to avoid Prometheus rejecting their duplication - if wanted in future they can be reenabled via `types=yes` and `help=yes`.