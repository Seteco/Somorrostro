![image7](https://cloud.githubusercontent.com/assets/2662304/14253734/536bd4c2-fa95-11e5-8872-81eed5178e4b.gif)

# Welcome to netdata!

> [![User Base](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=persons&label=user%20base&units=null&value_color=blue&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry) [![Monitored Servers](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=machines&label=servers%20monitored&units=null&value_color=orange&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry) [![Sessions Served](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_sessions&label=sessions%20served&units=null&value_color=yellowgreen&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry)<br/>_(the figures come from **[netdata registry](https://github.com/firehol/netdata/wiki/mynetdata-menu-item)** data, showing only installations that use the public global registry, counting since May 16th 2016)_
>
> [![New Users Today](http://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=persons&after=-86400&options=unaligned&group=incremental-sum&label=new%20users%20today&units=null&value_color=blue&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry) [![New Machines Today](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=machines&group=incremental-sum&after=-86400&options=unaligned&label=servers%20added%20today&units=null&value_color=orange&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry) [![Sessions Today](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_sessions&after=-86400&group=incremental-sum&options=unaligned&label=sessions%20served%20today&units=null&value_color=yellowgreen&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry)<br/>_Check **[[Generating Badges]]** for more information._

## latest news

> _There have been **significant improvements to alarms** in netdata since release 1.3.0. Alarms and notifications now support hysteresis, dynamic thresholds, self cancellation, they are clickable to lead you to the chart that raised the alarm, they can be sent as mobile phone push notifications (pushover.net), web browser push notifications, slack notifications, telegram.org notifications, they support roles with with multiple recipients and filtering based on severity per recipient. New alarms have been added and the thresholds and severities of all alarms have been improved to avoid unnecessary notifications._

These are the key changes since the last release of netdata:

- `Sep 18, 2016`<br/>netdata now collects **[extended TCP kernel statistics](https://github.com/firehol/netdata/pull/982)**. These are very important since they can be used to detect tunable parameters for low latency and best performance of TCP servers. _There is more work to be done to properly render all of them on the dashboard and add related alarms - if you can help to categorize all the TCP metrics into charts, comment on PR [#982](https://github.com/firehol/netdata/pull/982)_.

- `Sep 17, 2016`<br/>netdata can now **[detect port scans or busy daemons crashes by examining IPv4 TCP handshake statistics counters](https://github.com/firehol/netdata/issues/970)**.

- `Sep 17, 2016`<br/>netdata can now **[filter notifications based on their severity per recipient](https://github.com/firehol/netdata/pull/967)** (so that given recipients will only receive critical notifications, while others will receive all of them). This works for emails, pushover mobile notifications and slack channels' messages.

- `Sep 17, 2016`<br/>netdata can now [collect NFS statistics on **NFS Clients (v2, v3, v4)**](https://github.com/firehol/netdata/pull/968) (it was already able to collect NFS statistics on **NFS Servers (v2, v3, v4)**). Also, netdata now collects **IPv4 ICMP and UDPLite** statistics (it was already collecting generic IPv4 counters, TCP, UDP, fragments, etc).

- `Sep 16, 2016`<br/>netdata on **github's top projects for 2016**: **[https://octoverse.github.com/](https://octoverse.github.com/)**. Wow!

- `Sep 16, 2016`<br/>netdata now [collects **softnet** metrics that are used to indicate the source of network packet drops](https://github.com/firehol/netdata/pull/960). Using these metrics special alarms have been added to let you know how to fix a **dropped packets** problem.

- `Sep 15, 2016`<br/>netdata alarms now support alarm **notification hysteresis and dynamic thresholds**, to prevent alarm notification flood! With these features, netdata alarm notifications are now a lot more useful (self-cancelled alarms do not send notifications, flapping alarms do not produce a notification flood, etc).

- `Sep 10, 2016`<br/>netdata alarms can now **[push notifications to your mobile phone and post notifications to slack channels](https://github.com/firehol/netdata/wiki/health-monitoring#alarm-actions)**!

- `Sep 5, 2016`<br/>netdata alarms modal on the dashboard, has been improved significantly, now rendering all the information related to each alarm. Also web browser push notifications are now supported. The dashboard itself got a few improvements too.

- `Aug 28th, 2016`<br/>**[netdata 1.3.0 released](https://github.com/firehol/netdata/releases/tag/v1.3.0)**, which includes **[health monitoring - alarms](https://github.com/firehol/netdata/wiki/health-monitoring)**!


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

## What is it?

**Netdata** is a real-time performance and health monitoring solution.

Unlike other solutions that are only capable of presenting *statistics of past performance*, netdata is designed to be perfect for **real-time performance troubleshooting**.

Netdata is a linux daemon you run, that collects data in realtime (per second) and presents a web site to view and analyze them. The presentation is also real-time and full of interactive charts that precisely render all collected values.

Netdata has been designed to be installed **on every system**, without disrupting the applications running on it:

1. It will just use some spare CPU cycles (check **[[Performance]]**).
2. It will use the memory you want it have (check **[[Memory Requirements]]**).
3. Once started and while running, it does not use any disk I/O, apart its logging (check **[[Log Files]]**). Of course it saves its DB to disk when it exits and loads it back when it starts.

You can use it to monitor all your systems and applications. It will run on Linux PCs, servers or embedded devices.

Out of the box, it comes with plugins that collect key system metrics and metrics of popular applications.

---

## Why another monitoring tool?

The key goal of **netdata** is to help you achieve **operational excellence**.

To achieve that, it focuses on **real-time visualization** of what is happening on your systems or applications *now* and in the *recent past*.

**netdata** tries to visualize the truth of **now**, in its **greatest detail**, with detail comparable to the console tools!

---

## How it works?

You run a daemon on your linux: `netdata`. This daemon is written in C and is extremely lightweight. With less than *1% CPU utilization of a single core* (for the netdata core, plugins may use more), you get hundreds of charts and thousands of metrics, **all collected and visualized per second**.

**netdata**:

  - Spawns threads to collect all the data from all sources - it uses **[[Internal Plugins]]** and **[[External Plugins]]** for this.
  - Keeps track of the collected values in memory (no disk I/O at all, check **[[Memory Requirements]]**).
  - Is a standalone web server that serves its static files, for rendering its dashboards.
  - It provides a **[[REST API v1]]** for your browser to access the data.

If you install it on all your systems, each **netdata** will be standalone. There is no *central* netdata. Your web browser is the only entity that can *connect* all the netdata installations together. netdata dashboards can have charts from multiple netdata installations and these charts will still behave, on your browser, as if they were coming from the same netdata server!

In this image you can see netdata displaying charts from 2 servers. On the left is the demo site and on the right is a local installation of it (you have the same page on your netdata too, at `/tv.html`):

![tv](https://cloud.githubusercontent.com/assets/2662304/14262483/6d8500f8-fabe-11e5-84e1-c510b3bd6ebd.gif)

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
