
You need a working `sendmail` command for email alerts to work. Almost all MTAs provide a `sendmail` interface.

**[alarm-notify.sh](https://github.com/firehol/netdata/blob/master/plugins.d/alarm-notify.sh)** will send the emails from user `netdata` to the email address of the recipient.

edit `/etc/netdata/health_alarm_notify.conf` to configure recipients per role.

email notifications look like this:

![image](https://cloud.githubusercontent.com/assets/2662304/18407294/e9218c68-7714-11e6-8739-e4dd8a498252.png)