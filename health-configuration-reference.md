Everything related to health configuration is in `/etc/netdata/health.d`. In this directory you can put any number of files (in any number of sub-directories) with a suffix `.conf` to have them processed by netdata.

Health configuration can be reloaded at any time, without restarting netdata. Just send netdata the SIGUSR2 signal, like this:

```sh
killall -USR2 netdata
```

Reloading health configuration will re-do the actions of the alarms (i.e. will re-send emails for warning and critical alarms).

### Entities in the health files

There are 2 entities:

1. **alarms**, which are attached to specific charts, and 

2. **templates**, which define rules that should be applied to all charts having a specific `context`. You can use this feature to apply **alarms** to all disks, all network interfaces, all mysql databases, all nginx web servers, etc.

Both of these entities have exactly the same format and feature set. The only difference is the label `alarm` or `template`.

netdata supports overriding **templates** with **alarms**. For example, when a template is defined for a set of charts, an alarm with exactly the same name attached to the same chart the template matches, will have higher precedence (i.e. netdata will use the alarm on this chart and prevent the template from being applied to it).

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
    - `AFTER` is a relative number of seconds, but it also accepts a single letter for changing the units, like `-1s` = 1 second in the past, `-1m` = 1 minute in the past, `-1h` = 1 hour in the past, `-1d` = 1 day in the past. You need a negative number (i.e. how far in the past to look for the value). This is required.
    - `at BEFORE` is by default 0 and is not required. Using this you can define the end of the lookup. So data will be evaluated between `AFTER` and `BEFORE`.
    - `every DURATION` sets the updated frequency of the lookup (supports single letter units as above too).
    - `OPTIONS` can be `percentage`, `absolute`, `min2max`, `unaligned`. Check also the the badges documentation for more info.
    - `of DIMENSIONS` is optional and has to be the last parameter. Dimensions have to be separated by `,` or `|`. The space characters found in dimensions will be kept as-is (a few dimensions have spaces in their names).

  The result of the lookup will be available as `$this` and `$NAME` in expressions. The timestamps of the timeframe evaluated by the database lookup is available as variables `$after` and `$before` (both are unix timestamps).

- `green: NUMBER` and `red: NUMBER`

  Set the green and red thresholds of a chart. Both are available as `$green` and `$red` in expressions. If multiple alarms define different thresholds, the ones defined by the first alarm will be used. These will eventually visualized on the dashboard, so only one set of them is allowed. If you need multiple sets of them in different alarms, use absolute numbers instead of `$red` and `$green`.

- `every: DURATION`

  Sets the update frequency of this alarm.  This is the same to the `every DURATION` given in the `lookup` lines.

- `calc: EXPRESSION`

  This expression is evaluated just after the lookup. Its purpose is to apply some calculation before using the value looked up from the db. You can also have an expression without a lookup, using other variables that are available (so you can create new variables).

  The result of the calculation will be available as `$this` and `$NAME` in warning and critical expressions (overwriting the `lookup` one).

  Check expressions below for more information.

- `warn: EXPRESSION` and `crit: EXPRESSION`

  These expressions should evaluate to true or false (alternatively non-zero or zero). These trigger the alarm.

- `exec: SCRIPT`

  The script that will be executed when the alarm changes status.

- `to: ROLE`

  This will be the first parameter of the script to be executed. Its meaning is left up to the `exec` script. The default `exec` script, `alarm-notify.sh`, uses this field as a space separated list of roles, which are then consulted to find the exact recipients per notification method.

- `delay: [[[up U] [down D] multiplier M] max X]`

  This is used to provide optional hysteresis settings for the notifications, to defend against notification flood. These settings do not affect the actual alarm - only the time the `exec` script is executed.
  
  `up U` defines the delay to be applied to a notification for an alarm that raised its status (i.e. CLEAR to WARNING, CLEAR to CRITICAL, WARNING to CRITICAL). For example, `up 10s`, the notification for this event will be sent 10 seconds after the actual event. This is used in hope the alarm will get back to its previous state within the duration given. The default `U` is zero.
  
  `down D` defines the delay to be applied to a notification for an alarm that moves to lower state (i.e. CRITICAL to WARNING, CRITICAL to CLEAR, WARNING to CLEAR). For example, `down 1m` will delay the notification by 1 minute. This is used to prevent notifications for flapping alarms. The default `D` is zero.
  
  `mutliplier M` multiplies `U` and `D` when an alarm changes state, while a notification is delayed. The default multiplier is `1.0`.
  
  `max X`  defines the maximum absolute notification delay an alarm may get. The default `X` is `max(U * M, D * M)` (i.e. the max duration of `U` or `D` multiplied once with `M`).

  Example:

  `delay: up 10s down 15m multiplier 2 max 1h`

  The time is `00:00:00` and the status of the alarm is CLEAR.

  time of event|new status|delay|notification will be sent|why
  -------------|----------|:---:|-------------------------|---
  00:00:01 | WARNING | `up 10s` | 00:00:11 |first state switch
  00:00:05 | CLEAR |  `down 15m x2`| 00:30:05 |the alarm changes state while a notification is delayed, so it was multiplied
  00:00:06 | WARNING | `up 10s x2 x2` | 00:00:26 |multiplied twice
  00:00:07|CLEAR|`down 15m x2 x2 x2`|00:45:07|multiplied 3 times.

  So:
  -  `U` and `D` are multiplied by `M` every time the alarm changes state (any state, not just their matching one) and a delay is in place.
  - All are reset to their defaults when the alarm switches state without a delay in place.


### Expressions

netdata has an internal infix expression parser. This parses expressions and creates an internal structure that allows fast execution of them.

These operators are supported `+`, `-`, `*`, `/`, `<`, `<=`, `<>`, `!=`, `>`, `>=`, `&&`, `||`, `!`, `AND`, `OR`, `NOT`. Boolean operators result in either `1` (true) or `0` (false).

The conditional evaluation operator `?` is supported too. Using this operator IF-THEN-ELSE conditional statements can be specified. The format is: `(condition) ? (true expression) : (false expression)`. So, netdata will first evaluate the `condition` and based on the result will either evaluate `true expression` or `false expression`. Example: `($this > 0) ? ($avail * 2) : ($used / 2)`. Nested such expressions are also supported (i.e. `true expression` and `false expression` can contain conditional evaluations).

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

There are also a few special variables:

  - `this`, which is resolved to the value of the current alarm.
  - `status`, which is resolved to the current status of the alarm (the current = the last status, i.e. before the current database lookup and the evaluation of the `calc` line). This values can be compared with `$REMOVED`, `$UNINITIALIZED`, `$UNDEFINED`, `$CLEAR`, `$WARNING`, `$CRITICAL`. These values are incremental, ie. `$status > $CLEAL` works as expected.
  - `now`, which is resolved to current unix timestamp.


You can find all the variables that can be used for a given chart, using `http://your.netdata.ip:19999/api/v1/alarm_variables?chart=NAME`. This will dump all the indexes from the chart's perspective. Example: [variables for the `system.cpu` chart of the registry](https://registry.my-netdata.io/api/v1/alarm_variables?chart=system.cpu).

## Alarm Actions

The `exec` line in health configuration defines an external script that will be called once the alarm is triggered. The default script is **[alarm-notify.sh](https://github.com/firehol/netdata/blob/master/plugins.d/alarm-notify.sh)**. You can change the default script by editing `/etc/netdata/netdata.conf` (if you installation is old, you may need to download a fresh copy of it from netdata using the url `http://your.netdata:19999/netdata.conf).

`alarm-notify.sh` is capable of:

1. sending emails
2. sending pushover.net notifications (push notification to your mobile)
3. sending pushbullet.com notifications (push notifications to all devices linked within pushbullet account)
4. sending messages to slack.com channels
5. sending messages to Discord channels

It uses **roles**. For example `sysadmin`, `webmaster`, `dba`, etc.

Each alarm is assigned to one or more roles, using the `to` line of the alarm configuration. Then `alarm-notify.sh` uses its own configuration (`/etc/netdata/health_alarm_notify.conf` the default is [here](https://github.com/firehol/netdata/blob/master/conf.d/health_alarm_notify.conf)) to find the destination address of the notification for each method. Each role may have one or more destinations.

So, for example the `sysadmin` role may send:

1. emails to admin1@example.com and admin2@example.com
2. pushover.net notifications to USERTOKENS `A`, `B` and `C`.
3. pushbullet.com push notifications to admin1@example.com and admin2@example.com
4. messages to slack.com channel `#alarms` and `#systems`.
5. messages to Discord channels `#alarms` and `#systems`.

## Alarm Statuses

Alarms can have the following statuses:

  - `REMOVED` - the alarm has been deleted (this happens when a SIGUSR2 is sent to netdata to reload health configuration)
  - `UNINITIALIZED` - the alarm is not initialized yet
  - `UNDEFINED` - the alarm failed to be calculated (i.e. the database lookup failed, a division by zero occurred, etc)
  - `CLEAR` - the alarm is not armed / raised (i.e. is OK)
  - `WARNING` - the warning expression resulted in true or non-zero
  - `CRITICAL` - the critical expression resulted in true or non-zero

The external script will be called for all status changes.
