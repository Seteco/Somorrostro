Each netdata alarm can have its own alarm action.

The default alarm action is the script **[alarm-notify.sh](https://github.com/firehol/netdata/blob/master/plugins.d/alarm-notify.sh)**.

This script has the following features:

1. The `to` line of alarms can list one or more **roles**.
2. Each **role** can correspond to multiple **recipients**, using multiple **notification methods** (email, slack, discord, twilio, pageduty, etc).
3. Each **recipient** can filter severity (i.e. receive only critical notifications).

## configuration

Edit [`/etc/netdata/health_alarm_notify.conf`](https://github.com/firehol/netdata/blob/master/conf.d/health_alarm_notify.conf) to configure:

1. settings per notification method
2. recipients per **role** and **notification method**