
Live demo - **see it in action**: **[click here for live QoS charts, on our Server](http://netdata.firehol.org/#tc)**

![qos2](https://cloud.githubusercontent.com/assets/2662304/14439411/b7f36254-0033-11e6-93f0-c739bb6a1c3a.gif)


## Overview

One of the metrics missing in Linux monitoring, is bandwidth consumption for each open socket (inbound and outbound traffic). So, you cannot tell how much bandwidth your web server, your database server, your backup, your ssh sessions, etc are using.

To solve this problem, the most adventurous Linux monitoring tools install kernel modules to capture all traffic, analyze it and provide reports per application. A lot of work, CPU intensive and with a great degree of risk (due to the kernel modules involved which might affect the stability of the whole system). Not to mention that such solutions are probably better suited for a core linux router in your network.

Others use NFACCT, the netfilter accounting module which is already part of the Linux firewall. However, this would require configuring a firewall on every system you want to measure bandwidth (just FYI, I do install a firewall on every server - and I strongly advise you to do so too - but configuring accounting on it seems overkill when you don't really need it for other reasons - e.g. billing verification).

**There is however a much simpler approach**.

## QoS

One of the features the Linux kernel has, but it is rarely used, is its ability to **apply QoS on traffic**. Even most interesting is that it can apply QoS to **both inbound and outbound traffic**.

QoS is about 2 features:

1. **Classify traffic**

  Classification is the process of organizing traffic in groups, called **classes**. Classification can evaluate every aspect of traffic, like source and destination ports, source and destination IPs, netfilter marks, etc.

  When you classify traffic, you just assign a label to it. For example **I call `web server` traffic, the traffic from my server's tcp/80, tcp/443 and to my server's tcp/80, tcp/443, while I call `web surfing` all other tcp/80 and tcp/443 traffic**. You can use any combinations you like. There is no limit.

2. **Apply traffic shaping rules to these classes**

  Traffic shaping is used to control how network interface bandwidth should be shared among the classes. Of course we are not interested for this feature to just monitor the traffic. Classification will be enough for monitoring everything.

  I have to admit though, I usually apply QoS (including traffic shaping) to all my servers. The key reasons are:

    - ensure administrative tasks (like ssh, dns, etc) will always have a small but guaranteed bandwidth. So, no matter what, I will be able to ssh to my server and DNS will work.

    - ensure other administrative tasks will not monopolize all the available bandwidth. So, my nightly backup will not hurt my users, a developer that is copying files over the net will not get all the available bandwidth, etc.

    - ensure each end-user connection will get a fair cut of the available bandwidth.

Once **traffic classification** is applied, we can use **[netdata](https://github.com/firehol/netdata)** to visualize the bandwidth consumption per class in real-time (no configuration is needed for netdata - it will figure it out).

QoS, is extremely light. You will configure it once, and this is it. It will not bother you again.

---

## QoS in Linux? Have you lost your mind?

Yes I know... but no, I have not!

For sure, `tc` is one of the most, if not **the most**, undocumented, complicated and unfriendly system command in Linux. By the way, do you know that for matching a simple port range in `tc`, for example ports 1025 - 65535 you have to match these:

```
1025/0xffff
1026/0xfffe
1028/0xfffc
1032/0xfff8
1040/0xfff0
1056/0xffe0
1088/0xffc0
1152/0xff80
1280/0xff00
1536/0xfe00
2048/0xf800
4096/0xf000
8192/0xe000
16384/0xc000
32768/0x8000
```

what? Have you lost your mind?

This is why I wrote **[FireQOS](https://firehol.org/tutorial/fireqos-new-user/)**, a tool to simplify QoS management in Linux.

The **[FireHOL](https://firehol.org/)** package already distributes **[FireQOS](https://firehol.org/tutorial/fireqos-new-user/)**. Check the **[FireQOS tutorial](https://firehol.org/tutorial/fireqos-new-user/)** to learn how to write your own QoS configuration.

With **[FireQOS](https://firehol.org/tutorial/fireqos-new-user/)**, it is **really simple for everyone to use QoS in Linux**. Just install the package `firehol`. It should already be available for your distribution. If not, check the **[FireHOL Installation Guide](https://firehol.org/installing/)**. After that you will have the `fireqos` command which uses a configuration like the following:

## QoS Configuration

This is the file `/etc/firehol/fireqos.conf` we use at the netdata demo site:

```sh
    # configure the netdata ports
    server_netdata_ports="tcp/19999"

    interface eth0 world bidirectional ethernet balanced rate 50Mbit
       class arp
          match arp

       class icmp
          match icmp

       class dns commit 1Mbit
          server dns
          client dns

       class ntp
          server ntp
          client ntp

       class ssh commit 2Mbit
          server ssh
          client ssh

       class rsync commit 2Mbit max 10Mbit
          server rsync
          client rsync

       class web_server commit 40Mbit
          server http
          server netdata

       class client
          client surfing

       class nms commit 1Mbit
          match input src 10.2.3.5
```

Nothing more is needed. You just run `fireqos start` to apply this configuration, restart netdata and you have real-time visualization of the bandwidth consumption of your applications.

And this is what you are going to get:

![image](https://cloud.githubusercontent.com/assets/2662304/14436322/c91d90a4-0024-11e6-9fb1-57cdef1580df.png)

---

## More examples:

This is QoS from my home linux router. Check these features:

1. It is real-time (per second updates)
2. QoS really works in Linux - check that the `background` traffic is squeezed when `surfing` needs it.

![test2](https://cloud.githubusercontent.com/assets/2662304/14093004/68966020-f553-11e5-98fe-ffee2086fafd.gif)