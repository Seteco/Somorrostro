# Apache Plugin (apache)

The `apache` collector visualizes key performance data for an apache web server.

The source code is [here](https://github.com/firehol/netdata/blob/master/charts.d/apache.chart.sh).

## How it works

It runs `curl "http://apache.host/server-status?auto` to fetch the current status of apache.

It has been tested with apache 2.2 and apache 2.4. The latter also provides connections information (total and break down by status).

Apache 2.2 response:

```sh
$ curl "http://127.0.0.1/server-status?auto"
Total Accesses: 80057
Total kBytes: 223017
CPULoad: .018287
Uptime: 64472
ReqPerSec: 1.24173
BytesPerSec: 3542.15
BytesPerReq: 2852.59
BusyWorkers: 1
IdleWorkers: 49
Scoreboard: _________________________......................................._W_______________________.......................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................
```

Apache 2.4 response:

```sh
$ curl "http://127.0.0.1/server-status?auto"
127.0.0.1
ServerVersion: Apache/2.4.18 (Unix)
ServerMPM: event
Server Built: Dec 14 2015 08:05:54
CurrentTime: Saturday, 23-Jan-2016 14:42:06 EET
RestartTime: Saturday, 23-Jan-2016 04:57:13 EET
ParentServerConfigGeneration: 2
ParentServerMPMGeneration: 1
ServerUptimeSeconds: 35092
ServerUptime: 9 hours 44 minutes 52 seconds
Load1: 0.32
Load5: 0.32
Load15: 0.27
Total Accesses: 32403
Total kBytes: 34464
CPUUser: 30.37
CPUSystem: 29.55
CPUChildrenUser: 0
CPUChildrenSystem: 0
CPULoad: .170751
Uptime: 35092
ReqPerSec: .923373
BytesPerSec: 1005.67
BytesPerReq: 1089.13
BusyWorkers: 1
IdleWorkers: 99
ConnsTotal: 0
ConnsAsyncWriting: 0
ConnsAsyncKeepAlive: 0
ConnsAsyncClosing: 0
Scoreboard: __________________________________________________________________________________________W_________............................................................................................................................................................................................................................................................................................................
```

From the apache status output it collects:

 - total accesses (incremental value, rendered as requests/s)
 - total bandwidth (incremental value, rendered as bandwidth/s)
 - requests per second (this appears to be calculated by apache as an average for its lifetime, while the one calculated by netdata using the total accesses counter is real-time)
 - bytes per second (average for the lifetime of the apache server)
 - bytes per request (average for the lifetime of the apache server)
 - workers by status (`busy` and `idle`)
 - total connections (currently active connections - offered by apache 2.4+)
 - async connections per status (`keepalive`, `writing`, `closing` - offered by apache 2.4+)

## Configuration

The configuration is stored in `/etc/netdata/apache.conf`.

You can set:

 - `apache_url` to the URL your apache server is responding with mod_status information.

   The default is:

   ```
apache_url="http://127.0.0.1:80/server-status?auto"
```

 - `apache_update_every=NUMBER` to , to give the data collection frequency. The default is configured in netdata **[[Configuration]]**.


## Auto-detection

If you have configured your apache server to offer server-status information on localhost clients, the defaults should work fine.

## Apache Configuration

Apache configuration differs between distributions. Please check your distribution's documentation for information on enabling apache's `mod_status` module.

If you are able to run successfully, by hand this command:

```sh
curl "http://127.0.0.1:80/server-status?auto"
```

netdata will be able to do it too.
