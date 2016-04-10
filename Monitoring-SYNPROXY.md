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

---

## A note for DDoS testers

Since I posted this, several folks tried to run DDoS against http://netdata.firehol.org.

Well, guys **this site is not a test bed for DDoS**. Don't do this. We pay for the bandwidth and you are just wasting it!

Also, please try to understand what you are doing. SYNPROXY is about **spoofed packets**. Making a large set of POSTs or instructing exploited wordpress installations to attack the demo site, **is not a DDoS that SYNPROXY can detect**.

Next, http://netdata.firehol.org is behind cloudflare.com, so even if you manage to make a spoofed IPs attack, **you are actually attacking cloudflare.com**. Do not expect to see the traffic they detected as spoofed on the netdata demo site (you are reaching the demo site, through cloudflare proxies). You can see a real attack on the demo site, only if you know its real IP (but which, you guessed it - it only accepts requests from cloudflare.com). The demo site is just a demo for netdata, not a demo for DDoS.

And finally, thank you for exposing the IPs and hostnames of the exploited wordpress installations and the IPs of the hosts you manage to instruct them make so many POST requests to us.

Evidence of the attack:

 - [ngnix log with the exploited wordpress installations used today](https://iplists.firehol.org/netdata-attacks/wordpress_log-20160409-1816.txt)
 - [IPs of the exploited wordpress installations used today](https://iplists.firehol.org/netdata-attacks/wordpress_ips-20160409-1816.txt)
 - [ngnix log with the POST requests](https://iplists.firehol.org/netdata-attacks/post_log-20160409-1456.txt)
 - [IPs of the hosts that made the POST requests](https://iplists.firehol.org/netdata-attacks/post_ips-20160409-1456.txt)

You actually stressed netdata a bit.

This is what happened with the POSTs:

![image](https://cloud.githubusercontent.com/assets/2662304/14405861/ea5b9a48-fea0-11e5-8e24-9eb0506943d5.png)

And this is what happened with the wordpress attack:

![image](https://cloud.githubusercontent.com/assets/2662304/14405871/4d4c00de-fea1-11e5-9575-1fa70e8b1d25.png)

Now our nginx configuration includes these:

```
    if ($http_user_agent ~* "WordPress") {
        return 403;
    }

    if ($request_method !~ ^(GET|HEAD|OPTIONS)$ ) {
        return 403;
    }

    include firehol_webserver.conf;
    include netdata-attacks.conf;
```

`firehol_webserver` is [this IP Blacklist](http://iplists.firehol.org/?ipset=firehol_webserver) and `netdata-attacks` are the IPs given in the evidence above.

So, you are just blacklisted.

---

While these data exist in the netdata server, you can see what a DDoS means for a system (check CPU, interrupts, forks, context switches, memory, etc):

 - [direct link to the first attack](http://netdata.firehol.org/?force_before_ms=1460213988000&force_after_ms=1460213669000#netdata)

 - [direct link to the second attack](http://netdata.firehol.org/?force_before_ms=1460226227000&force_after_ms=1460225741000#netdata)
