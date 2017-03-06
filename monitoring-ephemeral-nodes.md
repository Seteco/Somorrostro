
# Real-time performance monitoring for ephemeral nodes

Auto-scaling is probably the most trendy service deployment strategy these days.

Auto-scaling detects the need for additional resources and boots VMs on demand, based on a template. Soon after they start running the applications, a load balancer starts distributing traffic to them, allowing the service to grow horizontally to the scale needed to handle the load. When demands falls, auto-scaling starts shutting down VMs that are no longer needed.

What a fantastic feature for controlling infrastructure costs! Pay only for what you need for the time you need it!

In auto-scaling, all servers are ephemeral, they live for just of few hours. Every VM is a brand new instance of the application, that was automatically created based on a template.

So, how can we monitor them? How can we be sure that everything is working as expected on all of them?

## the netdata way

We recently made a significant improvement at the core of netdata to support monitoring such setups.

Following the netdata way of monitoring, we wanted:

1. **real-time performance monitoring**, collecting **_thousands of metrics per server per second_**, visualized in interactive, automatically created dashboards.
2. **real-time alarms**, for all nodes.
3. **zero configuration**, all ephemeral servers should have exactly the same configuration, and nothing should be configured at any system for each of the ephemeral nodes. We shouldn't care if 10 or 100 servers are spawned to handle the load.
4. **self-cleanup**, so that nothing needs to be done for cleaning up the monitoring infrastructure from the hundreds of nodes that may have been monitored through time.

### how it works

All monitoring solutions, including netdata, work like this:

1. `collect metrics`, from the system and the running applications
2. `store metrics`, in a time-series database
3. `examine metrics` periodically, for triggering alarms and sending alarm notifications
4. `visualize metrics`, so that users can see what exactly is happening

netdata used to be self-contained, so that all these functions were handled entirely by each server. The changes we made, allow each netdata to be configured independently for each function. So, each netdata can now act as:

- a `self contained system`, much like it used to be.
- a `data collector`, that collects metrics from a host and pushes them to another netdata (with or without a local database and alarms).
- a `proxy`, that receives metrics from other hosts and pushes them immediately to other netdata servers. netdata proxies can also be `store and forward proxies` meaning that they are able to maintain a local database for all metrics passing through them (with or without alarms).
- a `time-series database` node, where data are kept, alarms are run and queries are served to visualise the metrics.

## configuring an auto-scaling setup

![netdata-for-ephemeral-nodes](https://cloud.githubusercontent.com/assets/2662304/23620490/bb950542-029f-11e7-89a3-848fc1692694.png)

You need a netdata `master` (the `central netdata` at the chart). This node should not be ephemeral. It will be the node where all ephemeral nodes (let's call them `slaves`) will be sending their metrics.

The master will need to authorize the slaves for accepting their metrics. This is done with an API key.

### API keys

API keys are just random GUIDs. Use the Linux command `uuidgen` to generate one. You can use the same API key for all your `slaves`, or you can configure one API for each of them. This is entirely your decision.

We suggest to use the same API key for each ephemeral node template you have, so that all replicas of the same ephemeral node will have exactly the same configuration.

I will use this API_KEY: `11111111-2222-3333-4444-555555555555`. Replace this with your own.

### configuring the `master`

On the master, edit `/etc/netdata/stream.conf` and set these:

```bash
[11111111-2222-3333-4444-555555555555]
	# enable/disable this API key
    enabled = yes
    
    # one hour of data for each of the slaves
    default history = 3600
    
    # do not save slave metrics on disk
    default memory = ram
    
    # alarms checks, only while the slave is connected
    health enabled by default = auto
```
*`stream.conf` on master, to enable receiving metrics from slaves using the API key.*

If you used many API keys, you can add one such section for each API key.

When done, restart netdata on the `master` node. It is now ready to receive metrics.

### configuring the `slaves`

On each of the slaves, edit `/etc/netdata/stream.conf` and set these:

```bash
[stream]
    # stream metrics to another netdata
    enabled = yes
    
    # the IP and PORT of the master
    destination = 10.11.12.13:19999
	
	# the API key to use
    api key = 11111111-2222-3333-4444-555555555555
```
*`stream.conf` on slaves, to enable pushing metrics to master at `10.11.12.13:19999`.*

Using just the above configuration, the `slaves` will be pushing their metrics to the `master` netdata, but they will still maintain a local database of the metrics and run health checks. To disable them, edit `/etc/netdata/netdata.conf` and set:

```bash
[global]
    # disable the local database
	memory mode = none

[health]
    # disable health checks
    enabled = no
```
*`netdata.conf` configuration on slaves, to disable the local database and health checks.*

Keep in mind that setting `memory mode = none` will also force `[health].enabled = no` (health checks require access to a local database). But you can keep the database and disable health checks if you need to.

### troubleshooting metrics streaming

Both the sender and the receiver of metrics log information at `/var/log/netdata/error.log`.

