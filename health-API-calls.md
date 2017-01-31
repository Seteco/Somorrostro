
- `http://your.netdata.ip:19999/api/v1/alarms?all` returns all the running/active alarms.
- `http://your.netdata.ip:19999/api/v1/alarms` returns all the currently raised alarms.
- `http://your.netdata.ip:19999/api/v1/alarm_log` returns all the events in the alarm log.
- `http://your.netdata.ip:19999/api/v1/alarm_log?after=UNIQUEID` returns all the events in the alarm log that occurred after UNIQUEID (you poll it once without `after=`, remember the last UNIQUEID of the returned set, which you give back to get incrementally the next events).
- `http://your.netdata.ip:19999/api/v1/badge.svg?alarm=NAME` returns an SVG (XML) of the given alarm NAME.
