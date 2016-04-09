![image6](https://cloud.githubusercontent.com/assets/2662304/14253733/53550b16-fa95-11e5-8d9d-4ed171df4735.gif)

See Linux Anti-DDoS **in action** at: **[netdata demo site (with SYNPROXY enabled)](http://netdata.firehol.org/#netfilter_synproxy)**.

---

## Linux Anti-DDoS

SYNPROXY is a TCP SYN packets proxy. It can be used to protect any TCP server (like a web server) from SYN floods and similar DDos attacks.

SYNPROXY is a netfilter module, in the Linux kernel (since version 3.12). It is optimized to handle millions of packets per second utilizing all CPUs available without any concurrency locking between the connections.

The net effect of this, is that the real servers will not notice any change during the attack. The valid TCP connections will pass through and served, while the attack will be stopped at the firewall.

To use SYNPROXY on your firewall, please follow our setup guides:

 - **[Working with SYNPROXY](https://github.com/firehol/firehol/wiki/Working-with-SYNPROXY)**
 - **[Working with SYNPROXY and traps](https://github.com/firehol/firehol/wiki/Working-with-SYNPROXY-and-traps)**

# Real-time monitoring of Linux Anti-DDoS Protection

As of today (Apr 9th, 2016) netdata is able to monitor in real-time (per second updates) the operation of the Linux Anti-DDoS protection.

It visualizes 4 charts:

1. TCP SYN Packets received on ports operated by SYNPROXY
2. TCP Cookies (valid, invalid, retransmits)
3. Connections Reopened
4. Entries used

Example image:

![ddos](https://cloud.githubusercontent.com/assets/2662304/14398891/6016e3fc-fdf0-11e5-942b-55de6a52cb66.gif)

## Note for DDoS testers

Since I posted this, several folks tried to run DDoS against http://netdata.firehol.org.

Well, guys **this site is not a test bed for DDoS**. Please don't do this.

If you are going to do it, please do it right. SYNPROXY is about spoofed packets. Instructing exploited wordpress installations to attack the demo site, **is not a DDoS SYNPROXY can detect**.

Next, http://netdata.firehol.org is behind cloudflare.com, so **you are actually attacking them**. You are not going to see the traffic they detected as spoofed on the demo site (you are reaching the demo site, through their proxies).

And finally, thank you for exposing the IPs and hostnames of the exploited wordpress installations to us.

Evidence of the attack:

 - [ngnix log with the exploited wordpress installations used today](https://iplists.firehol.org/exploited-wordpress/log.txt)
 - [IPs of the exploited wordpress installations used today](https://iplists.firehol.org/exploited-wordpress/ips.txt)

You actually stressed netdata a bit. Here it is:

![image](https://cloud.githubusercontent.com/assets/2662304/14405861/ea5b9a48-fea0-11e5-8e24-9eb0506943d5.png)

And here a few hours later:

![image](https://cloud.githubusercontent.com/assets/2662304/14405871/4d4c00de-fea1-11e5-9575-1fa70e8b1d25.png)

