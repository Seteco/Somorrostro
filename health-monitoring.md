
> New to netdata? Check its demo: **[http://my-netdata.io/](http://my-netdata.io/)**
>
> [![User Base](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=persons&label=user%20base&units=null&value_color=blue&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry) [![Monitored Servers](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=machines&label=servers%20monitored&units=null&value_color=orange&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry) [![Sessions Served](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_sessions&label=sessions%20served&units=null&value_color=yellowgreen&precision=0&v41)](https://registry.my-netdata.io/#netdata_registry)
> 
> [![New Users Today](http://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=persons&after=-86400&options=unaligned&group=incremental-sum&label=new%20users%20today&units=null&value_color=blue&precision=0&v40)](https://registry.my-netdata.io/#netdata_registry) [![New Machines Today](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_entries&dimensions=machines&group=incremental-sum&after=-86400&options=unaligned&label=servers%20added%20today&units=null&value_color=orange&precision=0&v40)](https://registry.my-netdata.io/#netdata_registry) [![Sessions Today](https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.registry_sessions&after=-86400&group=incremental-sum&options=unaligned&label=sessions%20served%20today&units=null&value_color=yellowgreen&precision=0&v40)](https://registry.my-netdata.io/#netdata_registry)

---

## table of contents

- [Overview](#netdata-got-health-monitoring)
- [Examples](#examples)
- [Health Configuration](#health-configuration)
  - [Entities in the Health files](#entities-in-the-health-files)
  - [The Format](#the-format)
  - [Expressions](#expressions)
  - [Variables](#variables)
- [Alarm Actions](#alarm-actions)
  - [web browser notifications](#web-browser-notifications)
  - [email messages](#alarm-emails)
  - [slack.com team collaboration](#slackcom-messages)
  - [pushover.net push notifications](#pushovernet-push-notifications)
  - [pushbullet.com push notifications](#pushbulletcom-push-notifications)
  - [telegram.org push messages](#telegramorg-messages)
- [Troubleshooting](#troubleshooting)

---

# netdata got health monitoring!

Dear dev-ops and sys-admins, **netdata got alarms**!

A few months ago, when [I decided to let the netdata users decide the features they need us to develop](https://github.com/firehol/netdata/issues/436), I was somewhat surprised that most users wanted **[health monitoring](https://github.com/firehol/netdata/issues/436#issuecomment-220832546)**.

I think I get it now.

Health monitoring is problematic for most people. I have not seen a single sys-adm or dev-op totaly happy with the tools he/she has.

So, I decided to build a health monitoring system in netdata that will overcome most of the problems other systems have:

Of course an alarm is just a threshold, like `A > 90`.

But netdata goes a lot beyond that...

netdata allows you to correlate different metrics. It is not just `A > 90`. It can be: `(A > 90 AND (B > 80 OR C < 40)) OR (D > 50 AND E < 30))`. This means for example that you can raise an alarm on the number of database requests only when the disk is congested, or when cpu utilization is too high too.

netdata allows you to take into account the values of the same metric **some time in the past**. So you can say: `A(now) > 90 AND A(30 mins ago) < 30`.

You can even calculate rates: `A(now) - A(30 mins ago) / (30 * 60)` = the rate A changes over the last 30 minutes. Then you can raise an alarm when: `A(rate of the last 30 mins) > 10`. This means you can detect if your web server is facing an abnormal request flood, or if your web server although operational is getting way too low requests.
  
You can calculate a percentage of change over the last period: `(A(now) - A(1 min ago)) * 100 / (A(1 min ago) - A(2 min ago))` = the percentage of the volume of the last minute compared to the volume of its previous minute. This means you can track your servers minute-by-minute and trigger alarms based on their changes.

netdata also allows you to evaluate simple expressions on any timeframe of a metric. For example, you can use in expressions the `min`, `max`, `average` and `sum` of any metric for any timeframe.

Then we come to configuration. All of you that use netdata already, know I hate configuration. I find absolutely no joy in configuring applications. Although netdata provides tons of configuration options, I always do my best so that most installations will need to configure nothing.

So, netdata comes with pre-defined alarms for detecting the most common problems. Out of the box:

- it will trigger alarms when the applications it monitors stop
- it will detect network interfaces errors
- it will detect disks not catching up with the load they are offered
- it will detect low disk space on any disk
- it will even **predict in how many hours your system is going to be out of disk space** and notify you if it is less than 48 hours.
- it will alert you if your system is running low on entropy (random numbers pool)

Even when you need to configure alarms by hand, netdata offers **Alarm Templates**. Once you have configured an alarm, netdata can apply this alarm on all similar charts/metrics. So, if for example, you build an alarm to detect a web server request flood, netdata can apply this alarm to all your web servers automatically.

netdata also offers **Context Based Variables**. When you configure an alarm to a chart, netdata automatically brings you as variables all the chart dimensions and all the dimensions and alarms of the charts that belong to the same family (e.g. family = `eth0` or `sda` or `mysql server 1`). This creates a context where similar things are available to be used with their "first" name.

netdata alarms are based on **expressions**. These expressions can use data from any chart, any dimension, any metric. If you want, you can correlate the backlog of the disk, to the number of database queries, to the packets rate of a network interface, to number of requests to a web server. This together with poweful **database lookup and reduce functions** allow you to create alarms for everything imaginable.

## Examples

Check the **[health.d directory](https://github.com/firehol/netdata/tree/master/conf.d/health.d)** for all alarms shipped with netdata.

Here are a few examples:

### Example 1

A simple check if an apache server is alive:

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

The above applies the **template** to all charts that have `context = apache.requests` (i.e. all your apache servers).

```
    calc: $now - $last_collected_t
```

`$now` is a standard variable that resolves to the current timestamp.
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

So, the warning condition checks if we have not collected data from apache for 5 iterations and the critical condition checks for 10 iterations.

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

So, the `calc` line finds the percentage of used space. `$this` resolves to this percentage.

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

  In the `calc` line: `$this` is the result of the `lookup` line (i.e. the free space 30 minutes ago) and `$avail` is the current disk free space. So the `calc` line will either have a positive number of GB/second if the disk if filling up, or a negative number of GB/second if the disk is freeing up space.

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

  the `calc` line estimates the time in hours, we will run out of disk space. Of course, only positive values are interesting for this check, so the warning and critical conditions check for positive values and that we have enough free space for 48 and 24 hours respectively.

  Once this alarm triggers we will receive an email like this:

  ![image](https://cloud.githubusercontent.com/assets/2662304/17839993/87872b32-6802-11e6-8e08-b2e4afef93bb.png)

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

Note that the drops chart does not exist if a network interface has never dropped a single packet. When netdata detects a dropped packet, it will add the chart and it will automatically attach this alarm to it.

---

## health configuration

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

  Set the green and red thresholds of a chart. Both are available as `$green` and `$red` in expressions.

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
  
  `up U` defines the delay to be applied to a notification for an alarm that raised its status (i.e. CLEAR to WARNING, CLEAR to CRITICAL, WARNING to CRITICAL). For example, `up 10s`, the notification for this event will be send 10 seconds after the actual event. This is used in hope the alarm will get back to its previous state within the duration given. The default `U` is zero.
  
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


## Alarm Actions

The `exec` line in health configuration defines an external script that will be called once the alarm is triggered. The default script is **[alarm-notify.sh](https://github.com/firehol/netdata/blob/master/plugins.d/alarm-notify.sh)**. You can change the default script by editing `/etc/netdata/netdata.conf` (if you installation is old, you may need to download a fresh copy of it from netdata using the url `http://your.netdata:19999/netdata.conf).

`alarm-notify.sh` is capable of:

1. sending emails
2. sending pushover.net notifications (push notification to your mobile)
3. sending pushbullet.com notifications (push notifications to all devices linked within pushbullet account)
4. sending messages to slack.com channels

It uses **roles**. For example `sysadmin`, `webmaster`, `dba`, etc.

Each alarm is assigned to one or more roles, using the `to` line of the alarm configuration. Then `alarm-notify.sh` uses its own configuration (`/etc/netdata/health_alarm_notify.conf` the default is [here](https://github.com/firehol/netdata/blob/master/conf.d/health_alarm_notify.conf)) to find the destination address of the notification for each method. Each role may have one or more destinations.

So, for example the `sysadmin` role may send:

1. emails to admin1@example.com and admin2@example.com
2. pushover.net notifications to USERTOKENS `A`, `B` and `C`.
3. pushbullet.com push notifications to admin1@example.com and admin2@example.com
4. messages to slack.com channel `#alarms` and `#systems`.

### Web Browser Notifications

The netdata dashboard shows HTML notifications, when it is open.
Such web notifications look like this:
![image](https://cloud.githubusercontent.com/assets/2662304/18407279/82bac6a6-7714-11e6-847e-c2e84eeacbfb.png)


### Alarm Emails

You need a working `sendmail` command for email alerts to work. Almost all MTAs provide a `sendmail` interface.

**[alarm-notify.sh](https://github.com/firehol/netdata/blob/master/plugins.d/alarm-notify.sh)** will send the emails from user `netdata` to the email address of the recipient.

edit `/etc/netdata/health_alarm_notify.conf` to configure recipients per role.

email notifications look like this:

![image](https://cloud.githubusercontent.com/assets/2662304/18407294/e9218c68-7714-11e6-8739-e4dd8a498252.png)

### Pushbullet.com push notifications 
Will look like this on your browser:
![image](https://lh3.googleusercontent.com/-EobKlar2aVKMwKMRJBmWT2ExK-tCN98KByItzo7fIBbNJY2UJ8FkgX5Ee_3CeHDz7EV0XF4=w1680-h885)

And like this on your Android device:


![image](https://lh4.googleusercontent.com/PJD20oVzxZtzGtRBQ8q2d0F6EUC3FZvJTUpnPUKWutacs9YHUKf_96YjEZl-zgvN3EAstATM=w1680-h885-rw)

You will need:

1. Signup and Login to pushbullet.com
2. Get your Access Token, go to https://www.pushbullet.com/#settings/account and create a new one
3. Fill in the PUSHBULLET_ACCESS_TOKEN with that value
4. Add the recipient emails to DEFAULT_RECIPIENT_PUSHBULLET
!!PLEASE NOTE THAT IF THE RECIPIENT DOES NOT HAVE A PUSHBULLET ACCOUNT, PUSHBULLET SERVICE WILL SEND AN EMAIL!!

Set them in `/etc/netdata/health_alarm_notify.conf`, like this:

```
###############################################################################
# pushbullet (pushbullet.com) push notification options

# multiple recipients can be given like this:
#                  "user1@email.com user2@mail.com"

# enable/disable sending pushover notifications
SEND_PUSHBULLET="YES"

# Signup and Login to pushbullet.com
# To get your Access Token, go to https://www.pushbullet.com/#settings/account
# And create a new access token
# Then just set the recipients emails
# Please note that the if the email in the DEFAULT_RECIPIENT_PUSHBULLET does
# not have a pushbullet account, the pushbullet service will send an email
# to that address instead

# Without an access token, netdata cannot send pushbullet notifications.
PUSBULLET_ACCESS_TOKEN="o.Sometokenhere"
DEFAULT_RECIPIENT_PUSHBULLET="admin1@example.com admin3@somemail.com"
```


### Slack.com messages

This is what you will get:
![image](https://cloud.githubusercontent.com/assets/2662304/18407116/bbd0fee6-7710-11e6-81cf-58c0defaee2b.png)

You need:

1. The **incoming webhook URL** as given by slack.com. You can use the same on all your netdata servers (or you can have multiple if you like - your decision).
2. One or more channels to post the messages to.

Get them here: https://api.slack.com/incoming-webhooks

Set them in `/etc/netdata/health_alarm_notify.conf`, like this:

```
###############################################################################
# sending slack notifications

# note: multiple recipients can be given like this:
#                  "CHANNEL1 CHANNEL2 ..."

# enable/disable sending pushover notifications
SEND_SLACK="YES"

# Login to slack.com and create an incoming webhook.
# You need only one for all your netdata servers.
# Without it, netdata cannot send slack notifications.
SLACK_WEBHOOK_URL="https://hooks.slack.com/services/XXXXXXXX/XXXXXXXX/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"

# if a role recipient is not configured, a notification will be send to
# this slack channel:
DEFAULT_RECIPIENT_SLACK="alarms"

```

You can define multiple channels like this: `alarms systems`.
You can give different channels per **role** using these (at the same file):

```
role_recipients_slack[sysadmin]="systems"
role_recipients_slack[dba]="databases systems"
role_recipients_slack[webmaster]="marketing development"
```

The keywords `systems`, `databases`, `marketing`, `development` are slack.com channels (they should already exist in slack).

### pushover.net push notifications

pushover.net allows you to receive push notifications on your mobile phone. The service seems free for up to 7.500 messages per month.

netdata will send warning messages with priority `0` and critical messages with priority `1`. pushover.net allows you to select do-not-disturb hours. The way this is configured, critical notifications will ring and vibrate your phone, even during the do-not-disturb-hours. All other notifications will be delivered silently.

You need:

1. APP TOKEN. You can use the same on all your netdata servers.
2. USER TOKEN for each user you are going to send notifications to. This is the actual recipient of the notification.

The configuration is like above (slack messages).

pushover.net notifications look like this:

![image](https://cloud.githubusercontent.com/assets/2662304/18407319/839c10c4-7715-11e6-92c0-12f8215128d3.png)


### telegram.org messages

[Telegram](https://telegram.org/) is a messaging app with a focus on speed and security, it’s super-fast, simple and free. You can use Telegram on all your devices at the same time — your messages sync seamlessly across any number of your phones, tablets or computers.

With Telegram, you can send messages, photos, videos and files of any type (doc, zip, mp3, etc), as well as create groups for up to 5000 people or channels for broadcasting to unlimited audiences. You can write to your phone contacts and find people by their usernames. As a result, Telegram is like SMS and email combined — and can take care of all your personal or business messaging needs.

netdata will send warning messages without vibration.

You need:

1. A bot token. To get one, contact the [@BotFather](https://telegram.me/BotFather) bot and send the command `/newbot`. Follow the instructions.
2. A chat id for every chat you want to send messages to. Contact the [@myidbot](https://telegram.me/myidbot) bot and send the command `/getid` to get your personal chat id or invite him into a group and issue the same command to get the group chat id.
3. Start a conversation with your bot or invite him into a group you want to sent messages to.

See slack for configuration.

Telegram messages look like this:

![image](https://fb.hash.works/ytl/preview.jpg)

### Alarm Statuses

Alarms can have the following statuses:

  - `REMOVED` - the alarm has been deleted (this happens when a SIGUSR2 is sent to netdata to reload health configuration)
  - `UNINITIALIZED` - the alarm is not initialized yet
  - `UNDEFINED` - the alarm failed to be calculated (i.e. the database lookup failed, a division by zero occurred, etc)
  - `CLEAR` - the alarm is not armed / raised (i.e. is OK)
  - `WARNING` - the warning expression resulted in true or non-zero
  - `CRITICAL` - the critical expression resulted in true or non-zero

The external script will be called for all status changes.

## Troubleshooting

Edit your netdata.conf and set `debug flags = 0x00800000`. Then check your `/var/log/netdata/debug.log`. It will show you how it works.

You can find the context of charts by looking up the chart in either `http://your.netdata:19999/netdata.conf` or `http://your.netdata:19999/api/v1/charts`.

You can find how netdata interpreted the expressions by examining the alarm at `http://your.netdata:19999/api/v1/alarms?all`. For each expression, netdata will return the expression as given in its config file, and the same expression with additional parentheses added to indicate the evaluation flow of the expression. 
