## Overview

One of the metrics missing in Linux monitoring, is bandwidth consumption for each open socket (inbound and outbound traffic). So, you cannot tell how much bandwidth your web server, your database server, your backup, you ssh sessions, etc are using.

To solve this problem, the most adventurous Linux monitoring tools install kernel modules to capture all traffic, analyze it and provide reports per application. A lot of work, CPU intensive and with a great degree of risk (due to the kernel modules involved which might affect the stability of the whole system). 

**There is however a much simpler approach**.

## QoS

One of the features the Linux kernel has, but it is rarely used, is its ability to **apply QoS on traffic**. Even most interesting is that it can apply QoS to **both inbound and outbound traffic**.

QoS is about 2 features:

1. **Classify traffic**

  Classification is the process of organizing traffic in groups, called **classes**. Classification can evaluate every aspect of traffic, like source and destination ports, source and destination IPs, netfilter marks, etc.

  When you classify traffic, you just assign a label to it. For example **I call `web server` traffic from my server's tcp/80, tcp/443 and to my server's tcp/80, tcp/443, while I call `web surfing` all other tcp/80 and tcp/443 traffic**. You can use any combinations you like.

2. **Apply traffic shaping rules to these classes**

  Traffic shaping is used to control how network interface bandwidth should be shared among the classes. Of course we are not interested for this feature to just monitor the traffic. Classification will be enough for monitoring everything.

  I have to admit though, I usually apply QoS (including traffic shaping) to all my servers. The key reasons are:

    - ensure administrative tasks (like ssh, dns, etc) will always have a small but guaranteed bandwidth. So, no matter what, I will be able to ssh to my server and DNS will work.

    - ensure other administrative tasks will not monopolize all the available bandwidth. So, my nightly backup will not hurt my users, a developer that is copying files over the net will not get all the available bandwidth, etc.

    - ensure each end-user connection will get a fair cut of the available bandwidth.

Once **traffic classification** is applied, we can use **netdata** to visualize the bandwidth consumption per class in real-time (no configuration is needed for netdata - it will figure it out).

### QoS in Linux? You must be joking...

Yes I know!

`tc` is one of the most (if not the most) undocumented, complicated and unfriendly system commands in Linux!

This is why I wrote **FireQOS**, a tool to simplify QoS management in Linux.

The [FireHOL](https://firehol.org/) suite already distributes `FireQOS`. Check the **[FireQOS tutorial](https://firehol.org/tutorial/fireqos-new-user/)** that explains how to write your own QoS configuration.

It is **really simple for everyone to apply QoS in Linux**. Check the configuration below.

Just install the package **firehol**. It should already be available for your distribution. If not, check the **[FireHOL Installation Guide](https://firehol.org/installing/)**.

## Example

You can see the result of the configuration below at the **[QoS charts at the netdata demo site](http://netdata.firehol.org/#tc)**. This is a screenshot of it:

![image](https://cloud.githubusercontent.com/assets/2662304/14436322/c91d90a4-0024-11e6-9fb1-57cdef1580df.png)

This how we configured it:

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

Nothing more is needed. You just run `fireqos start` to apply this configuration, restart netdata and you have real-time visualization of the bandwidth consumption of your applications.