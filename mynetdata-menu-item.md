# What is the `my-netdata` menu item?

This drop-down menu lists the netdata servers you have visited (except the one your are visiting now).


## Why?

netdata provides distributed monitoring.

Traditionally, monitoring solutions centralize all the data to provide unified dashboards across all servers. This has a few cons:

1. due to resources required, the number of metrics collected is very low.
2. for the same reason, the data collection frequency is not that frequent, at best it will be once every 10 or 15 seconds, at worst 5 or 10 mins.
2. the central monitoring solution can only *scale up*, thus becoming "another bottleneck" in the whole ecosystem. It also requires maintenance, administration, etc.
3. due to all the above, most centralized monitoring solutions are usually good for presenting *statistics of past performance* (i.e. cannot be used for performance troubleshooting).

Netdata has a different approach:

1. data collection happens per second
2. thousands of metrics per server are collected
3. data do not leave the server
4. netdata servers do not talk to each other
5. your browser connects all the netdata servers

Using netdata, your monitoring infrastructure is embedded on each server, without the need of additional resources. netdata is very resource efficient and utilizes server resources that already exist and are spare (on each server). This allows **scaling out** the monitoring infrastructure. 

However, the netdata approach introduces a few new issues that need to be addressed, one being **the list of netdata we have installed**, i.e. the URLs our netdata servers are listening.

To solve this, netdata provides a **registry**.

## What is the registry?

A registry has 3 entities:

1. **machines**: netdata installations (machine GUID)

  For each netdata installation it keeps track of the different URLs it has been accessed.

2. **persons**: web browsers accessing these netdata installations (person GUID)

  For each person, it keeps track the netdata installations it has accessed and their URLs.

3. **URLs** of netdata installations

  For each URL, it keeps nothing. It is just linked to persons and machines.

## Who talks to the registry?

Your web browser **only**!

Your netdata servers do not talk to the registry.

## What data the registry maintains?

Its database is **random person GUIDs**, **random machine GUIDs** and **URLs**. For persons and machines, it keeps links to URLs with 2 timestamps (first time seen, last time seen) and a counter (number of times it has been seen).

## Which is the default registry?

`registry.my-netdata.io` which is currently served by http://london.my-netdata.io

## Can this registry handle the global load of netdata installations?

Yeap! The registry supports 50.000 - 100.000 requests **per second per core**. It can do it...

## Every netdata can be a registry

Yes, you read correct, **every netdata can be a registry**. Just pick one and configure it.
