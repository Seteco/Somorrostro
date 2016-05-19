
**THIS IS A STUB PAGE WE CURRENTLY WRITE**

---

# Monitoring Disks with netdata

netdata is able to monitor performance and space metrics for all disks.

## disk names

netdata will automatically set the name of disks on the dashboard, from the mount point they are mounted, of course only when they are mounted. Changes in mount points are not currently detected (you will have to restart netdata to change the name of the disk).


## performance metrics

Performance metrics include:

1. -
2. -
3. -
4. ...

By default netdata will enable monitoring metrics only when they are not zero. If they are constantly zero they are ignored. Metrics that will start having values, after netdata is started, will be detected and charts will be automatically added to the dashboard (a refresh of the dashboard is needed for them to appear though).

netdata categorizes all block devices in 3 categories:

1. physical disks (i.e. block devices that does not have slaves and is not a partition)
2. virtual disks (i.e. block devices that have slaves - like RAID devices)
3. disk partitions (i.e. block devices that are use the same physical disk)

Performance metrics are enabled by default for all disk devices, except partitions. Partitions can be enabled by editing the netdata configuration.

### enabling partitions

You can enable either all partitions on all disks, or enable individual partitions.

#### enabling all partitions on all disks

1. edit `/etc/netdata/netdata.conf`
2. set ...

#### enabling individual partitions

To enable performance metrics for a specific partition:

1. edit `/etc/netdata/netdata.conf`
2. ...
3. ...
4. ...

## space metrics

