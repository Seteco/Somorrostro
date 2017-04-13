You need a working `sendmail` command for email alerts to work. Almost all MTAs provide a `sendmail` interface.

netdata sends all emails as user `netdata`, so make sure your `sendmail` works for local users.

email notifications look like this:

![image](https://cloud.githubusercontent.com/assets/2662304/18407294/e9218c68-7714-11e6-8739-e4dd8a498252.png)

You can configure recipients at this file and line: https://github.com/firehol/netdata/blob/99d44b7d0c4e006b11318a28ba4a7e7d3f9b3bae/conf.d/health_alarm_notify.conf#L101
This file should be installed in `/etc/netdata/`.