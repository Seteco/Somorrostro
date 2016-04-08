## Linux Anti-DDoS

SYNPROXY is a TCP SYN packets proxy. It can be used to protect any TCP server (like a web server) from SYN floods and similar DDos attacks.

SYNPROXY is a netfilter module, in the Linux kernel (since version 3.12). It is optimized to handle millions of packets per second utilizing all CPUs available without any concurrency locking between the connections.

The net effect of this, is that the real servers will not notice any change during the attack. The valid TCP connections will pass through and served, while the attack will be stopped at the firewall.

To use SYNPROXY on your firewall, please follow our **[Working with SYNPROXY](https://github.com/firehol/firehol/wiki/Working-with-SYNPROXY)** and **[Working with SYNPROXY and traps](https://github.com/firehol/firehol/wiki/Working-with-SYNPROXY-and-traps)** guides.

# Real-time performance monitoring of Linux Anti-DDoS Protection

As of today (Apr 9th, 2016) netdata is able to monitor in real-time (per second updates) the operation of the Linux Anti-DDoS protection.

It visualizes 4 charts:

1. TCP SYN Packets received on ports operated by SYNPROXY
2. TCP Cookies (valid, invalid, retransmits)
3. Connection Reopened
4. Entries used

You can see it live at our demo site: **[http://netdata.firehol.org](http://netdata.firehol.org/#netfilter_synproxy)**.

Example image:

![image](https://cloud.githubusercontent.com/assets/2662304/14398525/8029d936-fded-11e5-9478-d8c42def7865.png)
