Check: https://github.com/firehol/netdata/blob/master/conf.d/fping.conf

The fping plugin supports pinging a number of hosts and charting the results.

A recent, unreleased, version of `fping` is required. The supplied plugin can install it. Run:

```sh
/usr/libexec/netdata/plugins/fping.plugin install
```

The above will download, build and install the right version as `/usr/local/bin/fping`.

Then you need to edit `/etc/netdata/fping.conf` like this:

```sh
# uncomment the following line - it should already be there
fping="/usr/local/bin/fping"

# set here all the hosts you need to ping
# I suggest to use hostnames and put their IPs in /etc/hosts
hosts="host1 host2 host3"

# override the chart update frequency - the default is inherited from netdata
update_every=1

# time in milliseconds (1 sec = 1000 ms) to ping the hosts
# 200 = 5 pings per second
ping_every=200

# other fping options - these are the defaults
fping_opts="-R -b 56 -i 1 -r 0 -t 5000"
```

## multiple fping plugins with different settings

You may need to run multiple fping plugins with different settings for different hosts. For example, you may need to ping a few hosts 10 times per second, and others once per second.

netdata allows you to add as many `fping` plugins as you like.

Follow this procedure:

1. copy `fping.conf` to `fping2.conf`, in `/etc/netdata`
2. edit `fping2.conf` and set the settings and the hosts you need
3. soft link `fping.plugin` to `fping2.plugin`, in `/usr/libexec/netdata/plugins.d`.

That's it. netdata will detect the new plugin and start it.

You can name the new plugin any name you like. Just make sure the plugin and the configuration file have the same name.
