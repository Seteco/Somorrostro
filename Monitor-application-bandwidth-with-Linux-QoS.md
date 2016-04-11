## Overview

One of the metrics missing in Linux monitoring, is bandwidth consumption for each open socket (inbound and outbound traffic). So, you cannot tell how much bandwidth your web server, your database server, your backup, you ssh sessions, etc are using.

To solve this problem, the most adventurous Linux monitoring tools install kernel modules to capture all traffic, analyze it and provide reports per application. A lot of work, CPU intensive and with a great degree of risk (due to the kernel modules involved which might affect the stability of the whole system).

There is however a much simpler approach.

## QoS

One of the features the Linux kernel has, but it is rarely used, is its ability to **apply QoS on traffic**. Even most interesting is that it can apply QoS to **both inbound and outbound traffic**.

QoS has mainly 2 features:

1. Classify traffic

  Classification is the process of organizing the traffic in groups, called `classes`.

2. Apply traffic shaping rules to these `classes`

  Traffic Shaping is used to control how network interface bandwidth should be shared among the classes. Of course we are not interested for this feature to just monitor the traffic. Classification will be enough for monitoring everything.

I have to admit though, I usually apply QoS (including traffic shaping) to all my servers. The key reasons are:

- ensure administrative tasks (like ssh, dns, etc) will always have a small but guaranteed bandwidth
- ensure other administrative tasks will not monopolize all the available bandwidth (like backups)
- ensure each end-user connection will get a fair cut of the available bandwidth

Once **traffic classification** is applied, we can use **netdata** to visualize the bandwidth consumption per class in real-time (no configuration is needed for netdata - it will figure it out).

## Real Life example

You can see the result of the configuration below at the **[QoS charts at the netdata demo site](http://netdata.firehol.org/#tc)**. This is a screenshot of it:

![image](https://cloud.githubusercontent.com/assets/2662304/14436322/c91d90a4-0024-11e6-9fb1-57cdef1580df.png)

This how we configured it:

The FireHOL suite has already a program called `FireQOS` that manages QoS in Linux. Check the **[FireQOS tutorial](https://firehol.org/tutorial/fireqos-new-user/)** that explains how to write your own QoS rules.

This is the file `/etc/firehol/fireqos.conf` we use at the netdata demo site:

```sh
    interface eth0 world bidirectional ethernet balanced minrate 15kbit rate 1Gbit
       class arp
          match arp

       class icmp
          match icmp

       class dns
          server dns
          client dns

       class ntp
          server ntp
          client ntp

       class ssh
          server ssh
          client ssh

       class rsync
          server rsync
          client rsync

       class web_server
          server http

       class client
          client surfing

       class nms
          match input src 10.2.3.5
```

Nothing more is needed. You just run `fireqos start` to apply this configuration, restart netdata and you have real-time visualization of the bandwidth consumptions of your applications.
