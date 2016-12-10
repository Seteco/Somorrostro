
Yes, I know what you think. This cannot be happening.

Well, guess what... it does!

Let's see an example. Execute this in a shell:

```sh
while [ 1 ]; do ls -l /tmp >/dev/null; done
```

In most systems `/tmp` is a `tmpfs` device, so there is nothing that can stop this command from consuming entirely one of the CPU cores of the machine.

**None of the console performance monitoring tools can report that this command is using 100% CPU**. They do report of course that the CPU is busy, but **they fail to identify the process that consumes this CPU**.

This is because all the console tools report usage based on the processes found running at the moment they examine the process tree. So, they see just one `ls`. But the shell, is spawning hundreds of them, one after another (much like shell scripts do).

When I realized this fact, I got surprised. The Linux kernel reports to the parent process, the CPU time of processes that exit. However, the calculation to properly report the CPU time on each process, including its children that have exited, is quite complex.

In netdata, `apps.plugin` does this properly. So, let's see what netdata reports.

First, let's check the total CPU utilization of the system:

![image](https://cloud.githubusercontent.com/assets/2662304/21075487/1e86d1ca-bf1c-11e6-9c73-55ca8edb66b4.png)

And now, let's find out which applications netdata believes are using all this CPU:

![image](https://cloud.githubusercontent.com/assets/2662304/21075508/85b9ecb0-bf1c-11e6-8ab1-b7c3531762f4.png)

So, my `ssh` session is using 97% CPU time. `apps.plugin` groups all processes based on its configuration file (`/etc/netdata/apps_groups.conf`). The default configuration has nothing for `bash`, but it has for `sshd`, so netdata accumulates all ssh sessions to a dimension on the charts, called `ssh`. This includes all the processes in the process tree of `sshd`, including the exited children.

> Distributions based on `systemd`, provide another way to get cpu utilization per user session or service running: control groups, or cgroups. `apps.plugin` does not use these mechanisms. The process grouping made by `apps.plugin` works on any Linux, `systemd` based or not.

Let's see what the most famous console tools say:

## top

`top` reports that `bash` is using just 14%.

If you check the total system CPU utilization at the top, it says there is no idle CPU at all, but `top` fails to provide a breakdown of the CPU consumption in the system. To sum of the CPU utilization of all processes reported is 15.6%.

```
top - 18:46:28 up 3 days, 20:14,  2 users,  load average: 0.22, 0.05, 0.02
Tasks:  76 total,   2 running,  74 sleeping,   0 stopped,   0 zombie
%Cpu(s): 32.8 us, 65.6 sy,  0.0 ni,  0.0 id,  0.0 wa,  1.3 hi,  0.3 si,  0.0 st
KiB Mem :  1016576 total,   244112 free,    52012 used,   720452 buff/cache
KiB Swap:        0 total,        0 free,        0 used.   753712 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND                                                                                                                                                                    
12789 root      20   0   14980   4180   3020 S 14.0  0.4   0:02.82 bash                                                                                                                                                                       
    9 root      20   0       0      0      0 S  1.0  0.0   0:22.36 rcuos/0                                                                                                                                                                    
  642 netdata   20   0  132024  20112   2660 S  0.3  2.0  14:26.29 netdata                                                                                                                                                                    
12522 netdata   20   0    9508   2476   1828 S  0.3  0.2   0:02.26 apps.plugin                                                                                                                                                                
    1 root      20   0   67196  10216   7500 S  0.0  1.0   0:04.83 systemd                                                                                                                                                                    
    2 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kthreadd                                                                                                                                                                   
    3 root      20   0       0      0      0 S  0.0  0.0   0:00.00 ksoftirqd/0                                                                                                                                                                
    5 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kworker/0:0H                                                                                                                                                               
    7 root      20   0       0      0      0 S  0.0  0.0   0:12.72 rcu_sched                                                                                                                                                                  
    8 root      20   0       0      0      0 S  0.0  0.0   0:00.00 rcu_bh                                                                                                                                                                     
   10 root      20   0       0      0      0 S  0.0  0.0   0:00.00 rcuob/0                                                                                                                                                                    
   11 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 migration/0                                                                                                                                                                
   12 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 lru-add-drain                                                                                                                                                              
   13 root      rt   0       0      0      0 S  0.0  0.0   0:00.56 watchdog/0                                                                                                                                                                 
   14 root      20   0       0      0      0 S  0.0  0.0   0:00.00 cpuhp/0                                                                                                                                                                    
   15 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kdevtmpfs                                                                                                                                                                  
   16 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 netns                                                                                                                                                                      
   17 root      20   0       0      0      0 S  0.0  0.0   0:00.00 oom_reaper 
```

## htop

Exactly like `top`, `htop` is also providing an incomplete breakdown of the system CPU utilization.

```
  CPU[||||||||||||||||||||||||100.0%]   Tasks: 27, 11 thr; 2 running
  Mem[||||||||||||||||||||85.4M/993M]   Load average: 1.16 0.88 0.90 
  Swp[                         0K/0K]   Uptime: 3 days, 21:37:03

  PID USER      PRI  NI  VIRT   RES   SHR S CPU% MEM%   TIME+  Command
12789 root       20   0 15104  4484  3208 S 14.0  0.4 10:57.15 -bash
 7024 netdata    20   0  9544  2480  1744 S  0.7  0.2  0:00.88 /usr/libexec/netd
 7009 netdata    20   0  138M 21016  2712 S  0.7  2.1  0:00.89 /usr/sbin/netdata
 7012 netdata    20   0  138M 21016  2712 S  0.0  2.1  0:00.31 /usr/sbin/netdata
  563 root	     20   0  308M  202M  202M S  0.0 20.4  1:00.81 /usr/lib/systemd/
 7019 netdata    20   0  138M 21016  2712 S  0.0  2.1  0:00.14 /usr/sbin/netdata
  640 root       20   0 50228  8008  6356 S  0.0  0.8  0:01.19 /usr/lib/systemd/
 6625 root       20   0  144M  8208  6980 S  0.0  0.8  0:00.01 sshd: [accepted]
19947 root	     20   0 18932  3412  3028 R  0.0  0.3  0:00.08 htop
 7028 netdata    20   0 12072  3220  2876 S  0.0  0.3  0:00.15 bash /usr/libexec
 7015 netdata    20   0  138M 21016  2712 S  0.0  2.1  0:00.02 /usr/sbin/netdata
 1249 root       20   0 83140  3020  2168 S  0.0  0.3  0:06.32 /usr/sbin/sshd
    1 root       20   0 67196 10236  7500 S  0.0  1.0  0:05.03 /usr/lib/systemd/
  613 root       16  -4 55552  3368  3000 S  0.0  0.3  0:04.76 /sbin/auditd -n
F1Help  F2Setup F3SearchF4FilterF5Tree  F6SortByF7Nice -F8Nice +F9Kill  F10Quit

```

## atop

`atop` is also suffering from the same syndrome.

```
ATOP - localhost            2016/12/10  20:11:27    -----------      10s elapsed
PRC | sys    1.13s | user   0.43s | #proc     75 | #zombie    0 | #exit   5383 |
CPU | sys      67% | user     31% | irq       2% | idle      0% | wait      0% |
CPL | avg1    1.34 | avg5    1.05 | avg15   0.96 | csw    51346 | intr   10508 |
MEM | tot   992.8M | free  211.5M | cache 470.0M | buff   87.2M | slab  164.7M |
SWP | tot     0.0M | free    0.0M |              | vmcom 207.6M | vmlim 496.4M |
DSK |          vda | busy      0% | read       0 | write      4 | avio 1.50 ms |
NET | transport    | tcpi      16 | tcpo      15 | udpi       0 | udpo       0 |
NET | network      | ipi       16 | ipo       15 | ipfrw      0 | deliv     16 |
NET | eth0    ---- | pcki      16 | pcko      15 | si    1 Kbps | so    4 Kbps |

  PID SYSCPU USRCPU   VGROW  RGROW  RDDSK   WRDSK ST EXC  S  CPU CMD       1/600
12789  0.98s  0.40s      0K     0K     0K    336K --   -  S  14% bash
    9  0.08s  0.00s      0K     0K     0K      0K --   -  S   1% rcuos/0
 7024  0.03s  0.00s      0K     0K     0K      0K --   -  S   0% apps.plugin
 7009  0.01s  0.01s	     0K     0K     0K      4K --   -  S   0% netdata
    7  0.02s  0.00s      0K     0K     0K      0K --   -  S   0% rcu_sched
  563  0.00s  0.01s      0K    12K     0K    352K --   -  S   0% systemd-journa
  639  0.01s  0.00s     88K   248K     0K      0K --   -  R   0% atop
 7028  0.00s  0.01s      0K     0K     0K      0K --   -  S   0% bash
  613  0.00s  0.00s      0K     0K     0K      8K --   -  S   0% auditd
```

## glances

And the same is true for `glances`. The system's goes as 100%, but `glances` reports only 17% per process utilization. Note that being `python` program, `glances` uses quite some CPU while it runs.

```
localhost                                               Uptime: 3 days, 21:42:00

CPU  [100.0%]   CPU     100.0%   MEM     23.7%   SWAP      0.0%   LOAD    1-core
MEM  [ 23.7%]   user:    30.9%   total:   993M   total:       0   1 min:    1.18
SWAP [  0.0%]   system:  67.8%   used:    236M   used:        0   5 min:    1.08
                idle:     0.0%   free:    757M   free:        0   15 min:   1.00

NETWORK     Rx/s   Tx/s   TASKS  75 (90 thr), 1 run, 74 slp, 0 oth 
eth0        168b    2Kb
eth1          0b     0b     CPU%  MEM%   PID USER        NI S Command 
lo            0b     0b     13.5   0.4 12789 root         0 S -bash 
                             1.6   2.2  7025 root         0 R /usr/bin/python /u
DISK I/O     R/s    W/s      1.0   0.0     9 root         0 S rcuos/0
vda1           0     4K      0.3   0.2  7024 netdata      0 S /usr/libexec/netda
                             0.3   0.0     7 root         0 S rcu_sched
FILE SYS    Used  Total      0.3   2.1  7009 netdata      0 S /usr/sbin/netdata
/ (vda1)   1.56G  29.5G      0.0   0.0    17 root         0 S oom_reaper
                             0.0   0.0    19 root         0 S kcompactd0
                             0.0   0.2  1248 root         0 S /sbin/agetty --kee
                             0.0   0.0    88 root       -20 S ipv6_addrconf
                             0.0   0.8 12813 root         0 S sshd: root [priv]
                             0.0   0.0   121 root       -20 S deferwq
                             0.0   0.3   638 chrony       0 S /usr/sbin/chronyd
                             0.0   0.0    27 root       -20 S md
                             0.0   0.3  1249 root         0 S /usr/sbin/sshd 
                             0.0   0.0    10 root         0 S rcuob/0
                             0.0   1.0     1 root         0 S /usr/lib/systemd/s
                             0.0   0.0     3 root         0 S ksoftirqd/0
                             0.0   0.0    86 root       -20 S kpsmoused
                             0.0   0.0   227 root       -20 S scsi_tmf_2
                             0.0   0.0    79 root       -20 S kthrotld


2016-12-10 20:14:18       No warning or critical alert detected
```

## Conclusion

From a performance monitoring perspective, the lack of properly identifying the CPU consumption used by processes, is terrifying. All tools I found, fail to properly report where the CPU goes.

In several cases, this leads to wrong conclusions. For example, I use several **pacemaker** clusters. Before netdata, I was not aware that the clustering software itself requires so some CPU:

pacemaker running on server A:

![image](https://cloud.githubusercontent.com/assets/2662304/21076003/3ebf7fac-bf29-11e6-9953-272518a15876.png)

`top` reports only 2% for `lrmd`, while it consumes 35% on the average, with spikes even above 100% (100% = 1 core).

Similarly, pacemaker running on server B:

![image](https://cloud.githubusercontent.com/assets/2662304/21076040/2870d3b2-bf2a-11e6-9fa8-eee228be116e.png)

