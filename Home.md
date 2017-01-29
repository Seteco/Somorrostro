![image7](https://cloud.githubusercontent.com/assets/2662304/14253734/536bd4c2-fa95-11e5-8872-81eed5178e4b.gif)

# Welcome to netdata!

> [![User Base](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=persons&label=user%20base&units=null&value_color=blue&precision=0&v42)](https://registry.my-netdata.io/#netdata_registry) [![Monitored Servers](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=machines&label=servers%20monitored&units=null&value_color=orange&precision=0&v42)](https://registry.my-netdata.io/#netdata_registry) [![Sessions Served](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_sessions&label=sessions%20served&units=null&value_color=yellowgreen&precision=0&v42)](https://registry.my-netdata.io/#netdata_registry)<br/>_(the figures come from **[netdata registry](https://github.com/firehol/netdata/wiki/mynetdata-menu-item)** data, showing only installations that use the public global registry, counting since May 16th 2016)_
>
> [![New Users Today](http://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=persons&after=-86400&options=unaligned&group=incremental-sum&label=new%20users%20today&units=null&value_color=blue&precision=0&v42)](https://registry.my-netdata.io/#menu_netdata_submenu_registry) [![New Machines Today](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=machines&group=incremental-sum&after=-86400&options=unaligned&label=servers%20added%20today&units=null&value_color=orange&precision=0&v42)](https://registry.my-netdata.io/#menu_netdata_submenu_registry) [![Sessions Today](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_sessions&after=-86400&group=incremental-sum&options=unaligned&label=sessions%20served%20today&units=null&value_color=yellowgreen&precision=0&v42)](https://registry.my-netdata.io/#menu_netdata_submenu_registry)<br/>_Check **[[Generating Badges]]** for more information._

## latest news

<p align="center">
Netdata is featured at <b><a href="https://octoverse.github.com/" target="_blank">GitHub's State Of The Octoverse 2016</a></b><br/>
<a href="https://octoverse.github.com/" target="_blank"><img src="https://cloud.githubusercontent.com/assets/2662304/21743260/23ebe62c-d507-11e6-80c0-76b95f53e464.png"/></a>
</p>


`Jan 22nd, 2017` - **[netdata v1.5.0 released!](https://github.com/firehol/netdata/releases)**

 - netdata now runs on **FreeBSD** and **MacOS**
 - netdata now supports **Graphite**, **OpenTSDB**, **Prometheus** and compatible backends
 - netdata now monitors **systemd Services**
 - new plugins: fping, postgres, varnish, elasticsearch, haproxy, freeradius, mdstat, ISC dhcpd, fail2ban, openvpn, NUMA memory, CPU Idle States, gunicorn, ECC memory errors, IPC semaphores, uptime
 - improved plugins: netfilter conntrack, mysql/mariadb, ipfs, cpufreq, hddtemp, sensors, nginx, nginx_log, phpfpm, redis, dovecot, containers and cgroups, disk space, apps.plugin, tc (QoS) and almost all internal plugins (memory, IPv4 and IPv6, network interfaces, QoS, etc)
 - dozens of new and improved alarms (including performance monitoring alarms for mysql)
 - new alarm notifications: messagebird.com, pagerduty.com, pushbullet.com, twilio.com, hipchat, kafka
 - dozens more improvements and performance optimizations

## demo sites

Live demo installations of netdata are available at **[http://netdata.rocks](http://netdata.rocks)**:

Location |  netdata demo URL | 60&nbsp;mins&nbsp;reqs | VM Donated by
:-------:|:-----------------:|:----------:|:-------------
London (UK)|**[london.netdata.rocks](http://london.netdata.rocks)**<br/>(this is the global netdata **registry** and has **named** and **mysql** charts)|[![Requests Per Second](http://london.netdata.rocks/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&after=-3600&options=unaligned&group=sum&label=reqs&units=empty&value_color=blue&precision=0&v42)](http://london.netdata.rocks)|[DigitalOcean.com](https://m.do.co/c/83dc9f941745)
Atlanta (USA)|**[atlanta.netdata.rocks](http://atlanta.netdata.rocks)**<br/>(with **named** and **mysql** charts)|[![Requests Per Second](http://atlanta.netdata.rocks/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&after=-3600&options=unaligned&group=sum&label=reqs&units=empty&value_color=blue&precision=0&v42)](http://atlanta.netdata.rocks)|[CDN77.com](https://www.cdn77.com/)
Bangalore (India)|**[bangalore.netdata.rocks](http://bangalore.netdata.rocks)**|[![Requests Per Second](http://bangalore.netdata.rocks/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&after=-3600&options=unaligned&group=sum&label=reqs&units=empty&value_color=blue&precision=0&v42)](http://bangalore.netdata.rocks)|[DigitalOcean.com](https://m.do.co/c/83dc9f941745)
Frankfurt (Germany)|**[frankfurt.netdata.rocks](http://frankfurt.netdata.rocks)**|[![Requests Per Second](http://frankfurt.netdata.rocks/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&after=-3600&options=unaligned&group=sum&label=reqs&units=empty&value_color=blue&precision=0&v42)](http://frankfurt.netdata.rocks)|[DigitalOcean.com](https://m.do.co/c/83dc9f941745)
New York (USA)|**[newyork.netdata.rocks](http://newyork.netdata.rocks)**|[![Requests Per Second](http://newyork.netdata.rocks/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&after=-3600&options=unaligned&group=sum&label=reqs&units=empty&value_color=blue&precision=0&v42)](http://newyork.netdata.rocks)|[DigitalOcean.com](https://m.do.co/c/83dc9f941745)
San Francisco (USA)|**[sanfrancisco.netdata.rocks](http://sanfrancisco.netdata.rocks)**|[![Requests Per Second](http://sanfrancisco.netdata.rocks/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&after=-3600&options=unaligned&group=sum&label=reqs&units=empty&value_color=blue&precision=0&v42)](http://sanfrancisco.netdata.rocks)|[DigitalOcean.com](https://m.do.co/c/83dc9f941745)
Singapore|**[singapore.netdata.rocks](http://singapore.netdata.rocks)**|[![Requests Per Second](http://singapore.netdata.rocks/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&after=-3600&options=unaligned&group=sum&label=reqs&units=empty&value_color=blue&precision=0&v42)](http://singapore.netdata.rocks)|[DigitalOcean.com](https://m.do.co/c/83dc9f941745)
Toronto (Canada)|**[toronto.netdata.rocks](http://toronto.netdata.rocks)**|[![Requests Per Second](http://toronto.netdata.rocks/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&after=-3600&options=unaligned&group=sum&label=reqs&units=empty&value_color=blue&precision=0&v42)](http://toronto.netdata.rocks)|[DigitalOcean.com](https://m.do.co/c/83dc9f941745)

*Netdata dashboards are mobile and touch friendly.*

## Installation

Want to set it up on your systems now? Jump to **[[Installation]]**.

---

## A welcome note

Welcome. I am [@ktsaou](https://github.com/ktsaou), the founder of [firehol.org](http://firehol.org) and [my-netdata.io](https://my-netdata.io).

netdata is **a scalable, distributed, real-time, performance and health monitoring solution** for Linux, FreeBSD and MacOS. It is **open-source** too.

Out of the box, it collects 1k to 5k metrics **per server per second**. It is the corresponding of running: `top`, `vmstat`, `iostat`, `iotop`, `sar`, `systemd-cgtop` and a dozen more console tools running in parallel. netdata is very efficient in this: the daemon needs just 1% to 3% cpu of a single core, even when it runs on IoT.

Many people view netdata as a  `collectd` + `graphite` + `grafana` alternative, or compare it with `cacti` or `munin`. All these are really great tools, but they are not netdata. Let's see why.

My primary goal when I was designing netdata was to help us find why our systems and applications are slow or misbehaving. To provide a system that could kill the console for performance monitoring.

To do this, I decided that:

- **high resolution metrics** is more important than long history
- **the more metrics collected, the better** - we should not fear to add 1k metrics more
- effective monitoring starts with **monitoring everything about each node**

Enterprises usually have dedicated resources and departments for collecting and analyzing system and application metrics at similar resolution and scale. netdata attempts to offer this functionality to everyone, without the dedicated resources - of course within limits.

For big setups, netdata can [archive its metrics](https://github.com/firehol/netdata/wiki/netdata-backends) to `graphite`, `opentsdb`, `prometheus` and all compatible ones (`kairosdb`, `influxdb`, etc). This allows even enterprises with dedicated departments and infrastructure, to use netdata for data collection and real-time alarms.

netdata alarms can be setup on any metric or combination of metrics and can send notifications to:

- email addresses
- slack channels
- discord channels
- pushover
- pushbullet
- telegram.org
- pagerduty
- twilio
- messagebird

Alarms are role based (each alarm can go to one or more roles), roles are multi-recipient and multi-channel (i.e. `sysadmin` = several email recipients + pushover) and each recipient may filter severity. You can also add more notification methods quite easily ([it is a shell script](https://github.com/firehol/netdata/blob/master/plugins.d/alarm-notify.sh)).

The number of metrics collected by netdata provides very interesting alarms. Install netdata and run this:

```sh
while [ 1 ]; do telnet HOST 12345; done
```

where `HOST` is your default gateway (`12345` is a random non existing port). It will not work of course. But leave it running for a few seconds. You will get an alert that your system is receiving an abnormally high number of TCP resets. If `HOST` is also running netdata, you will receive another alert there, that the system is sending an abnormally high number of TCP resets. This means that if you run a busy daemon and it crashes, you will get notified, although netdata knows nothing specific about it.

Of course netdata is young and still far from a complete monitoring solution that could replace everything.
We work on it... patience...

## What is it?

**netdata** is *scalable, distributed real-time performance and health monitoring*:

### distributed
A **netdata** should be installed on each of your servers. It is the equivalent of a monitoring agent, as provided by all other monitoring solutions. It runs everywhere a Linux kernel runs: PCs, servers, embedded devices, IoT, etc.

Netdata is very resource efficient and you can control its resource consumption. It will use:

- some spare CPU cycles, usually just 1-3% of a single core (check **[[Performance]]**),
- the RAM you want it have (check **[[Memory Requirements]]**), and
- no disk I/O at all, apart its logging (check **[[Log Files]]**). Of course it saves its DB to disk when it exits and loads it back when it starts.

### scalable
Unlike traditional monitoring solutions that move all the metrics collected on all servers, to a central place, **netdata** keeps all the data on the server they are collected.

This allows **netdata** to collect _thousands of metrics **per second**_ on each server.

When you use **netdata**, adding 10 more servers or collecting 10000 more metrics does not have any measurable impact on the monitoring infrastructure or the servers they are collected. This provides virtually **unlimited scalability**.

**netdata** collected metrics can be pushed to central time-series databases (like `graphite` or `opentsdb`) for archiving (check **[[netdata backends]]**), and **netdata** can push these data at a lower frequency/detail to allow these servers scale, but this is not required. It exists only for long-term archiving of the data and **netdata** never uses these databases as a data source.

### real-time
Everything **netdata** does is **per-second** so that the dashboards presented are just a second behind reality, much like the console tools do. Of course, when [**netdata** is installed on weak IoT devices](https://github.com/firehol/netdata/wiki/Performance#running-netdata-in-embedded-devices), this frequency can be lowered, to control the CPU utilization of the device.

**netdata** is adaptive. It adapts its internal structures to the system it runs, so that the repeating task of data collection is performed utilizing the minimum of CPU resources.

The web dashboards are also real-time and interactive. **netdata** achieves this, by splitting to work load, between the server and the dashboard client. Each server is collecting the metrics and maintaining a very fast round-robin database in memory, while providing basic data manipulation tasks (like data reduction functions), while each web client accessing these metrics is taking care of everything for data visualization. The result is:

- minimum CPU resources on the servers
- fully interactive real-time web dashboards, with some CPU pressure on the web browser while the dashboard is shown.

### performance monitoring
**netdata** collects and visualizes metrics. If it is a number and it can be collected somehow, netdata can visualize it. Out of the box, it comes with plugins that collect hundreds of system metrics and metrics of [popular applications](https://github.com/firehol/netdata/wiki/Add-more-charts-to-netdata).

### health monitoring
**netdata** provides powerful [alarms and notifications](https://github.com/firehol/netdata/wiki/health-monitoring). It comes preconfigured with dozens of alarms to detect common health and performance issues and it also accepts custom alarms defined by you.

---

# Documentation

This wiki is the whole of it. Other than the wiki, currently there is the... source code.

You should at least walk through the pages of the wiki. They have a good overview of netdata, what it can do and how to use it.

---

# Support

If you need help, please use the github issues section.

---


# FAQ

## Is it ready?

Software is never ready. There is always something to improve.

Netdata is stable. We use it on production systems without any issues.

## Is it released?

Yeap! Check the [releases page](https://github.com/firehol/netdata/releases).

## Why you wrote data collection?

Well, there are plenty of data collectors already. But we have one or more of the following problems with them:

- They are not able for per second data collection
- They can do per second data collection, but they are not optimized enough for always running on all systems
- They need to be configured, when we need auto-detection

Of course, we could use them just to get data at a slower rate, and this can be done, but it was not our priority. netdata proves that **real-time data collection and visualization can be done efficiently**.

## Is it practical to have so short historical data?

For a few purposes yes, for others no.

Our focus is **real-time data collection and visualization**. Our (let's say) "competitors" are the console tools, neither grafana nor collectd, statsd, nagios, zabbix, etc. All these are perfect tools for what they do (and they do a lot). But we think they provide "statistics about past performance" (of course with alarms, health monitoring, etc). netdata provides "real-time performance monitoring", much like the console tools do. Different things.

Of course, historical data is our next priority.

## Why there is no "central" netdata?

We strongly believe monitoring should be scaled out, not up. A "central" monitoring server is just another problem and should be avoided. Of course it is needed for health monitoring, but for real-time performance monitoring it will just add delays and eventually destroy the whole idea.

We all have a wonderful tool on our desktops, that connects us to the entire world: the **web browser**! This is the "central" netdata that connects all the netdata installations. We have done a lot of work towards this and we believe we are very close to show you what we mean. Patience...

## Can I help?

Of course! Please do!

As with all open source projects, **the more people using it, the better the project is**. So give it a github star, post about it on facebook, twitter, reddit, google+, hacker news, etc. Spreading the word costs you nothing and helps the project improve. It is the minimum you should give back for using a project that has thousands of hours of work in it and you get it for free.

Also important is to **open github issues** for the things that are not working well for you. This will help us make netdata better.

These are others areas we need help:

- _Can you code?_

  - you can **write plugins for data collection**. This is very easy and any language can be used.
  - you can **write dashboards**, specially optimised for monitoring the applications you use.

- _Can you write documentation?_

  - We have left the wiki open for anyone to edit. If any area needs further details, you can edit that page. (of course we monitor all edits - so please try to contribute and not destroy things).

- _Do you have special skills?_

  - are you a **marketing** guy? Help us setup a **social media strategy** to build and grow the netdata community.
  - are you a **devops** guy? Help us setup and maintain the public global servers.
  - are you a **linux packaging** guy? Help us **distribute pre-compiled packages** of netdata for all major distributions, or help netdata be included in official distributions.

## Is there a roadmap?

These are what we currently work on (in that order):

1. Finish packaging for the various distros.

2. Add health monitoring (alarms, notifications, etc)

3. More plugins - a lot more plugins!

 - monitor more applications (hadoop and friends, postgres, etc)
 - rewrite the netfilter plugin to use libnlm.
 - allow internal plugins to be forked to external processes (this will protect the netdata daemon from plugin crashes, allow different security schemes for each plugin, etc).

4. Improve the memory database (possibly using an internal deduper, compression, disk archiving, mirroring it to third party databases, etc).

5. Invent a flexible UI to connect multiple netdata server together. We have done a lot of progress with the registry and the `my-netdata` menu, but still there are a lot more to do.

5. Document everything (this is a work in progress already).

There are a lot more enhancements requested from our users (just navigate through the issues to get an idea).
Enhancements like authentication on UI, alarms and alerts, etc will fit somehow into this list.
Patience...
