# netdata got health monitoring!


## overview

netdata health moniroting is quite powerful. It can be used to create monitoring alarms on any chart, any dimension, any metric collected by netdata:

1. alarms can examine current and past data, including data reduction functions (min, max, average, sum).
2. alarms can examine meta-data (like the last collected time)
3. alarms can use **expressions** to:
  - **set variables** that can be used in other alarms
  - **evaluate** warning and critical conditions
4. alarms can be escalated and demoted
5. **templates of alarms** are supported (i.e. alarms for all disks, all network interfaces, all apache servers, all squid servers, all nginx servers, all redis servers, etc).

netdata ships with alarms that detect many anomalies.

## health configuration

Everything related to health configuration is in `/etc/netdata/health.d`. In this directory you can put any number of files (in any number of sub-directories) with a suffix `.conf` to have them processed by netdata.

Health configuration can be reloaded at any time, without restarting netdata. Just send netdata the SIGUSR2 signal, like this:

```sh
killall -USR2 netdata
```

Reloading health configuration will re-do the actions of the alarms (i.e. will re-send emails for warning and critical alarms).

## Entities in the health files

There are 2 entities:

1. **alarms**, which are attached to specific charts, and 

2. **templates**, which define rules that should be applied to all charts having a specific `context`. You can use this feature to apply **alarms** to all disks, all network interfaces, all mysql databases, all nginx web servers, etc.

Both of these entities have exactly the same format and feature set. The only difference is the label `alarm` or `template`.

### The format

The following lines are parsed:

- `alarm: NAME` or `template: NAME`

  This line has to be first on each alarm or template. `NAME` is anything you would like to name it (the only symbols allowed are `.` and `_`).

- `on: CHART` (for alarms) or `on: CONTEXT` (for templates)

  This line defines the data the alarm should be attached to.

  For `CHART` you can use a chart `id` or `name`, as shown on the dashboard.

  `CONTEXT` is the template of a chart. For example the charts `mysql_local.net` and `mysql_server2.net` have the same context: `mysql.net`. So, you can use this to apply alarms to all `mysql.net` charts.

  To find the `CONTEXT` of a chart download the latest `netdata.conf`, find the chart you are interested and you will see its context in its configuration section. You can also find it if you fetch `http://your.netdata:19999/api/v1/charts` (again search for the chart and you will find its context).

- `lookup: METHOD AFTER [at BEFORE] [every DURATION] [OPTIONS] [of DIMENSIONS]`

  This makes a database lookup to find a value. Everything is the same with [badges](https://github.com/firehol/netdata/wiki/Generating-Badges). In short:

    - `METHOD` is one of `average`, `min`, `max`, `sum`, `incremental-sum`. This is required.
    - `AFTER` is a relative number of seconds, but it also accepts a single letter for changing the units, like `1s` = 1 second, `1m` = 1 minute, `1h` = 1 hour, `1d` = 1 day. You probably need a negative number of `AFTER` (i.e. how far in the past to look for the value). This is required.
    - `at BEFORE` is by default 0 and is not required. Using this you can lookup data some time in the past.
    - `every DURATION` sets the updated frequency of the lookup (supports single letter units as above too).
    - `OPTIONS` can be `percentage`, `absolute`, `min2max`, `unaligned`. Check also the the badges documentation for more info.
    - `of DIMENSIONS` is optional and has to be the last parameter. Dimensions have to be separated by `,` or `|`. The space characters found in dimensions will be keps as-is (a few dimensions have spaces in their names).

  The result of the lookup will be available as `$this` and `$NAME` in expressions.

- `green: NUMBER` and `red: NUMBER`

  Set the green and red thresholds of a chart. Both are available as `$green` and `$red` in expressions.

- `every: DURATION`

  Sets the update frequency of this alarm.  This is the same to the `every DURATION` given in the `lookup` lines.

- `calc: EXPRESSION`

  This expression is evaluated just after the lookup. Its purpose is to apply some calculation before using the value looked up from the db. You can also have an expression without a lookup, using other variables that are available (so you can also create variables).

  The result of the calculation will be available as `$this` and `$NAME` in expressions (overwriting the `lookup` ones).

  Check expressions below for more information.

- `warn: EXPRESSION` and `crit: EXPRESSION`

  These expressions should evaluate to true or false (alternatively non-zero or zero). These trigger the alarm.

- `exec: SCRIPT`

  The script that will be executed when the alarm changes status.

  Alarms can have the following statuses:

  - `UNINITIALIZED` - the alarm is not initialized yet
  - `UNDEFINED` - the alarm failed to be calculated (i.e. the database lookup failed, a division by zero occured, etc)
  - `CLEAR` - the alarm is not armed
  - `WARNING` - the warning expression resulted in true or non-zero
  - `CRITICAL` - the critical expression resulted in true or non-zero

  The `SCRIPT` will be called with a number of parameters. Check the **[alarm-email.sh](https://github.com/firehol/netdata/blob/master/plugins.d/alarm-email.sh)** script that ships with netdata for an example.

### Expressions

netdata has an internal infix expression parser. This parses expressions and creates an internal structure that allows fast execution of them.

These operators are supported `+`, `-`, `*`, `/`, `<`, `<=`, `<>`, `!=`, `>`, `>=`, `&&`, `||`, `!`, `AND`, `OR`, `NOT`. Boolean operators result in either `1` (true) or `0` (false).

Expressions also support the `abs()` function.

Expressions can have variables. Variables start with `$`. Check below for more information.

There are two special values you can use:

  - `nan`, for example `$this != nan` will check if the variable `this` is available. A variable can be `nan` if the database lookup failed. All calculations (i.e. addition, multiplication, etc) with a `nan` result in a `nan`. netdata will log an error if any `calc`, `warn` or `crit` expressions result in `nan`.

  - `inf`, for example `$this != inf` will check if `this` is not infinite. A value or variable can be infinite if divided by zero. All calculations (i.e. addition, multiplication, etc) with a `inf` result in a `inf`. netdata will log an error if any `calc`, `warn` or `crit` expressions result in `inf`.

### Variables

netdata supports 3 new internal indexes for variables that will be used in health monitoring:

  - **chart local variables**. All the dimensions of the chart are exposed as local variables. All chart alarms are exposed too.

  charts define a few special variables:

   - `last_collected_t` is the unix timestamp of the last data collection
   - `collected_total_raw` is the sum of all the dimensions (their last collected values)
   - `update_every` is the update frequency of the chart
   - `green` and `red` the threshold defined in alarms (these are per chart - the charts inherits them from the the first alarm that defined them)

  dimensions define their last calculated (i.e. interpolated) value, exactly as shown on the charts, but also a variable with their name and suffix `_raw` that resolves to the last collected value - as collected and another with suffix `_last_collected_t` that resolves to unix timestamp the dimension was last collected (there may be dimensions that fail to be collected while others continue normaly).

  - **family variables**. Families are used to group charts together. For example all `eth0` charts, have `family = eth0`. This index includes all local variables, but if there are overlaping variables, only the first are exposed.

  - **host variables**. All the dimensions of all charts, including all alarms, in fullname. Fullname is `CHART.VARIABLE`, where `CHART` is either the chart id or the chart name (both are supported).

There is also a few special variables:

  - `this`, which is resolved to the value of the current alarm
  - `now`, which is resolved to current unix timestamp

## Examples

There are many alarms shipped with netdata already. Check the **[health.d directory](https://github.com/firehol/netdata/tree/master/conf.d/health.d)**

### Example 1

Check if an apache server is alive:

```
template: apache_last_collected_secs
      on: apache.requests
    calc: $now - $last_collected_t
   every: 10s
    warn: $this > ( 5 * $update_every)
    crit: $this > (10 * $update_every)
```

The above checks that netdata is able to collect data from apache. In detail:

```
template: apache_last_collected_secs
```

The above defines a **template** named `apache_last_collected_secs`. The name is important since `$apache_last_collected_secs` resolves to the `calc` line. So, try to give something descriptive.

```
      on: apache.requests
```

The above applies the **template** to all charts that have `context = apache.requests`.

```
    calc: $now - $last_collected_t
```

`$now` is a standard variable that resolved to the current timestamp.
`$last_collected_t` is the last data collection timestamp of the chart. So this calculation gives the number of seconds passed since the last data collection.

```
   every: 10s
```

The alarm will be evaluated every 10 seconds.

```
    warn: $this > ( 5 * $update_every)
    crit: $this > (10 * $update_every)
```

If these result in non-zero or true, they trigger the alarm.

`$this` refers to the value of this alarm (i.e. the result of the `calc` line. We could also use `$apache_last_collected_secs`.

`$update_every` is the update frequency of the chart, in seconds.

So, the warning condition checks if we have not collected data from apache for 5 iterations, which the critical conditions checks for 10 iterations.

### Example 2

Check if any of the disks is critically low on disk space:

```
template: disk_full_percent
      on: disk.space
    calc: $used * 100 / ($avail + $used)
   every: 1m
    warn: $this > 80
    crit: $this > 95
```

`$used` and `$avail`  are the `used` and `avail` chart dimensions as shown on the dashboard.

So, the `calc` line find the percentage of used space. `$this` resolves to this percentage.

### Example 3

Predict if any disk will run out of space in the near future.

We do this in 2 steps:

1. Calculate the disk fill rate

  ```
template: disk_fill_rate
      on: disk.space
  lookup: max -1s at -30m unaligned of avail
    calc: ($this - $avail) / (30 * 60)
   every: 15s
   ```

  In the `calc` line: `$this` is the result of the `lookup` line (i.e. the free space 30 minutes before) and `$avail` is the current disk free space. So the `calc` line will either have a positive number of bytes/second if the disk if filling up, or a negative number of bytes/second if the disk is freeing up space.

  There is no `warn` or `crit` lines here. So, this template will just do the calculation and nothing more.

2. Predict the hours after which the disk will run out of space

   ```
template: disk_full_after_hours
      on: disk.space
    calc: $avail / $disk_fill_rate / 3600
   every: 10s
    warn: $this > 0 and $this < 48
    crit: $this > 0 and $this < 24
   ```

  the `calc` line estimates the time in hours, we will run out of disk space. Of course, only positive values are interesting for this check, so the warning and critical conditions check that we have enough free space if the rate at which we fill the disk gives us at least 48 or 24 hours respectively.

### Example 4

Check if any network interface is dropping packets:

```
template: 30min_packet_drops
      on: net.drops
  lookup: sum -30m unaligned absolute
   every: 10s
    crit: $this > 0
```

The `lookup` line will calculate the sum of the all dropped packets in the last 30 minutes.

The `crit` line will issue a critical alarm if even a single packet has been dropped.


## Tracing health monitoring

Edit your netdata.conf and set `debug flags = 0x00800000`. Then check your `/var/log/netdata/debug.log`. It will show you how it works.
