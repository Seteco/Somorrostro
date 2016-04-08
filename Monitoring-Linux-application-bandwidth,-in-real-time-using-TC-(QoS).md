# Overview

Per application bandwidth monitoring is Linux, is problematic. The Linux kernel does not expose counters for the bandwidth its processes send or receive.

So how can we get bandwidth charts like this:

XXX

The solution is very simple:

1. Use FireQOS to classify traffic, using the ports each application uses
2. Use Netdata to visualize them in real-time

You can install these 2, on every server and / or a Linux router that all the traffic passed through. Usually I do both. I install a basic FireQOS configuration on each server (no need for actual traffic shaping - just classification) and a more complex FireQOS configuration at the core routers of my DMZ.

Let's see an example:

## FireQOS

FireQOS is part of FireHOL. If you install FireHOL v3+ from your linux distribution package manager, you will have FireQOS too.

FireQOS is just used to setup `tc` classes and filters. Everything is handled by the Linux kernel, so you don't need to run a daemon. You configure it, you run it just once, and this is it.

You need a FireQoS configuration file (`/etc/firehol/fireqos.conf`) like this:

```

interface 

```

You can apply it using the command:

```sh
fireqos start
```

You can monitor
