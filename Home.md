![image7](https://cloud.githubusercontent.com/assets/2662304/14253734/536bd4c2-fa95-11e5-8872-81eed5178e4b.gif)

# Welcome to netdata!

> June 4th, 2016: Netdata can generate badges:
> 
> [![User Base](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=persons&label=user%20base&units=null&value_color=blue&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry)
> [![Monitored Servers](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=machines&label=servers%20monitored&units=null&value_color=orange&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry)
> [![Sessions Served](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_sessions&label=sessions%20served&units=null&value_color=yellowgreen&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry)
>
> (the figures come from **[netdata registry](https://github.com/firehol/netdata/wiki/mynetdata-menu-item)** data, counting since May 16th 2016)
>
> [![New Users Today](http://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=persons&after=-86400&options=unaligned&group=incremental-sum&label=new%20users%20today&units=null&value_color=blue&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry)
> [![New Machines Today](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=machines&group=incremental-sum&after=-86400&options=unaligned&label=servers%20added%20today&units=null&value_color=orange&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry)
> [![Sessions Today](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_sessions&after=-86400&group=incremental-sum&options=unaligned&label=sessions%20served%20today&units=null&value_color=yellowgreen&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry)
>
> Check **[[Generating Badges]]** for more information.

## Demo Sites

Live demo installations of netdata are available at **[netdata.rocks](http://netdata.rocks)**:

Location |  netdata demo URL | requests/s | VM Donated by
:-------:|:-----------------:|:----------:|:-------------
Atlanta (USA)|**[atlanta.netdata.rocks](http://atlanta.netdata.rocks)** (this **named** and **mysql** stats)|[![Requests Per Second](http://atlanta.netdata.rocks/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&label=now&units=%5cs&value_color=blue&precision=0&v41b)](http://atlanta.netdata.rocks)|Donated by [CDN77.com](https://www.cdn77.com/) and located at their awesome CDN network
Bangalore (Idia)|**[bangalore.netdata.rocks](http://bangalore.netdata.rocks)**|[![Requests Per Second](http://bangalore.netdata.rocks/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&label=now&units=%5cs&value_color=blue&precision=0&v41)](http://bangalore.netdata.rocks)|Donated by [DigitalOcean.com](https://m.do.co/c/83dc9f941745) and located at their excellent Cloud Computing services
Frankfurt (Germany)|**[frankfurt.netdata.rocks](http://frankfurt.netdata.rocks)**|[![Requests Per Second](http://frankfurt.netdata.rocks/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&label=now&units=%5cs&value_color=blue&precision=0&v41)](http://frankfurt.netdata.rocks)|Donated by [DigitalOcean.com](https://m.do.co/c/83dc9f941745) and located at their excellent Cloud Computing services
London (UK)|**[london.netdata.rocks](http://london.netdata.rocks)** (this is also the global registry and has **named** and **mysql** stats)|[![Requests Per Second](http://london.netdata.rocks/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&label=now&units=%5cs&value_color=blue&precision=0&v41)](http://london.netdata.rocks)|Donated by [DigitalOcean.com](https://m.do.co/c/83dc9f941745) and located at their excellent Cloud Computing services
New York (USA)|**[newyork.netdata.rocks](http://newyork.netdata.rocks)**|[![Requests Per Second](http://newyork.netdata.rocks/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&label=now&units=%5cs&value_color=blue&precision=0&v41)](http://newyork.netdata.rocks)|Donated by [DigitalOcean.com](https://m.do.co/c/83dc9f941745) and located at their excellent Cloud Computing services
San Francisco (USA)|**[sanfrancisco.netdata.rocks](http://sanfrancisco.netdata.rocks)**|[![Requests Per Second](http://sanfrancisco.netdata.rocks/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&label=now&units=%5cs&value_color=blue&precision=0&v41)](http://sanfrancisco.netdata.rocks)|Donated by [DigitalOcean.com](https://m.do.co/c/83dc9f941745) and located at their excellent Cloud Computing services
Singapore|**[singapore.netdata.rocks](http://singapore.netdata.rocks)**|[![Requests Per Second](http://singapore.netdata.rocks/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&label=now&units=%5cs&value_color=blue&precision=0&v41)](http://singapore.netdata.rocks)|Donated by [DigitalOcean.com](https://m.do.co/c/83dc9f941745) and located at their excellent Cloud Computing services
Toronto (Canada)|**[toronto.netdata.rocks](http://toronto.netdata.rocks)**|[![Requests Per Second](http://toronto.netdata.rocks/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&label=now&units=%5cs&value_color=blue&precision=0&v41)](http://toronto.netdata.rocks)|Donated by [DigitalOcean.com](https://m.do.co/c/83dc9f941745) and located at their excellent Cloud Computing services

Netdata dashboards are mobile and touch friendly.

## Installation

Want to set it up on your systems now? Jump to **[[Installation]]**.

---

## What is it?

**Netdata** is a real-time performance monitoring solution.

Unlike other solutions that are only capable of presenting *statistics of past performance*, netdata is designed to be perfect for **real-time performance troubleshooting**.

Netdata is a linux daemon you run, which collects data in realtime (per second) and presents a web site to view and analyze them. The presentation is also real-time and full of interactive charts that precisely render all collected values.

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

So, netdata is: **non disruptive, real-time performance monitoring and visualization, in the greatest possible detail**.

---

## How it works?

You run a daemon on your linux: `netdata`. This daemon is written in C and is extremely lightweight. With less than 1% CPU utilization of a single core (for the netdata core, plugins may use more), you get hundreds of charts and thousands of metrics, **all collected and visualized per second**.

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

## Can I code too?

Of course! Fork the repo, adapt as you see fit and create pull requests.
If you want to discuss your plans, open a github issue to start the discussion.

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


## Is there a roadmap?

These are what we currently work on (in that order):

1. Finish packaging for the various distros.

2. More plugins - a lot more plugins!

 - monitor cgroups (containers) performance and utilization from the host
 - monitor more of the system
 - monitor more applications (hadoop and friends, postgres, etc)
 - re-write BASH data collectors (squid, mysql, etc) to node.js or python
 - rewrite the netfilter plugin to use libnlm.
 - allow internal plugins to be forked to external processes (this will protect the netdata daemon from plugin crashes, allow different security schemas for each plugin, etc).

3. Improve the memory database (possibly using an internal deduper, compression, disk archiving, mirroring it to third party databases, etc).

4. Invent a flexible UI to connect multiple netdata server together

5. Document everything (this is a work in progress already).

There are a lot more enhancements requested from our users (just navigate through the issues to get an idea).
Enhancements like authentication on UI, alarms and alerts, etc will fit somehow into this list.
Patience...