# What is the `my-netdata` menu item?

This drop-down menu lists the netdata servers you have visited (except the one your are visiting now).


## Why?

netdata provides distributed monitoring.

Traditionally, monitoring solutions centralize all the data to provide unified dashboards across all servers. This has a few cons:

1. the number of metrics collected are very limited
2. the central monitoring solution can only *scale up*, thus becoming another important component of the whole ecosystem
3. due to the above, most metrics collected by centralized monitoring solutions are collected too late to be of any use other than *statistics of past performance*

Netdata has a different approach:

1. data collection happens per second
2. thousands of metrics per server are collected
3. data do not leave the server they belong
4. netdata servers do not talk to each other
5. your browser connects all the netdata servers

The netdata approach allows scaling out the monitoring infrastructure. netdata is very resource efficient and utilizes server resources that already exist and are spare (on each server). Using netdata, your monitoring infrastructure is embedded on each server, without the need of additional resources.

However, the netdata approach introduces a few different issues, once being **the list of netdata we have installed**, the URLs our netdata servers are listening.

To solve this, netdata provides a **registry**.

## What is the registry?

## Who talks to the registry?

## What data the registry maintains?

## Every netdata can be your registry
