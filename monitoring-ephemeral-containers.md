netdata monitors containers automatically when it is installed at the host, or when it is installed in a container that has access to the `/proc` and `/sys` filesystems of the host.

netdata prior to v1.6 had 2 issues when such containers were monitored:

1. network interface alarms where triggering when containers were stopped

2. charts were never cleaned up, so after some time dozens of containers were showing up on the dashboard, and they were occupying memory.


## the current netdata

network interfaces and cgroups (containers) are now self-cleaned.

So, when a network interface or container stops, netdata might log a few errors in error.log complaining about files it cannot find, but immediately:

1. it will detect this is a removed container or network interface
2. it will freeze/pause all alarms for them
3. it will mark their charts as obsolete
4. obsolete charts are not be offered on new dashboard sessions (so hit F5 and the charts are gone)
5. existing dashboard sessions will continue to see them, but of course they will not refresh
6. obsolete charts will be removed from memory, 1 hour after the last user viewed them (configurable with `[global].cleanup obsolete charts after seconds = 3600` (at netdata.conf).
7. when obsolete charts are removed from memory they are also deleted from disk (configurable with `[global].delete obsolete charts files = yes`)
