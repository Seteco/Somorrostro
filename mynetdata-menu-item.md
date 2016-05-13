# What is the `my-netdata` menu item?

This drop-down menu lists the netdata servers you have visited (except the one your are visiting now).


## Why?

netdata provides distributed monitoring.

Traditionally, monitoring solutions centralize all the data to provide unified dashboards across all servers. This has a few cons:

1. the number of metrics collected are very limited.
2. the data collection frequency is not that frequent, at best it will be once every 10 or 15 seconds, at worst 5 mins.
2. the central monitoring solution can only *scale up*, thus becoming "another problem" in the whole ecosystem.
3. due to the above, most centralized monitoring solutions are usually good for presenting *statistics of past performance*.

Netdata has a different approach:

1. data collection happens per second
2. thousands of metrics per server are collected
3. data do not leave the server
4. netdata servers do not talk to each other
5. your browser connects all the netdata servers

Using netdata, your monitoring infrastructure is embedded on each server, without the need of additional resources. This allows **scaling out** the monitoring infrastructure. netdata is very resource efficient and utilizes server resources that already exist and are spare (on each server). 

However, the netdata approach introduces a few new issues that need to be addressed, one being **the list of netdata we have installed**, i.e. the URLs our netdata servers are listening.

To solve this, netdata provides a **registry**.

## What is the registry?

A registry tracks 2 entities

1. netdata installations (machine GUID)

  For each netdata installation it keeps track of the different URLs it has been accessed.

2. users accessing these netdata installations (person GUID)

  For each user, it keeps track the netdata installations he/she has accessed and their URLs.

## Who talks to the registry?

Your browser only! Your netdata installations do not talk to the registry.

## What data the registry maintains?

Its database is **person GUIDs**, **machine GUIDs** and **URLs**. For these, it keeps 2 timestamps (first time seen, last time seen) and a counter (number of times it has been seen).

## Which is the default registry?

`registry.my-netdata.io` which is currently served by http://london.my-netdata.io

## Every netdata can be a registry
