# Charts.d

Charts.d is BASH script that allows the data collection using simple scripts.

It has been designed so that the actual script that will do data collection will be permanently in memory, collecting data with as little overheads as possible (i.e. initialize once, repeatedly collect values with minimal overhead).

Charts.d looks for scripts in `/usr/libexec/netdata/charts.d`. The scripts should have the filename suffix: `.chart.sh`.

## Charts.d configuration

Charts.d itself can be configured using the configuration file `/etc/netdata/charts.d.conf`. This file is also a BASH script.

In this file, you can place statements like this:

```
X=yes
Y=no
```

where `X` and `Y` are the names of individual charts.d collector scripts.

When set to `yes`, charts.d will evaluate the collector script (see below). When set to `no`, charts.d will ignore the collector script.


## A charts.d collector

A charts.d collector is a BASH script defining a few functions.

For a collector called `X`, the following criteria must be met:

1. The collector script must be called `X.chart.sh` and placed in `/usr/libexec/netdata/charts.d`.

2. If the collector script needs a configuration, it should be called `X.conf` and placed in `/etc/netdata`. The configuration file `X.conf` is also a BASH script itself.

3. All functions and global variables defined in the script and its configuration, must begin with `X_`.

4. The following functions must be defined:

   - `X_check()` - returns 0 or 1 depending on whether the collector is able to run or not (following the standard Linux command line return codes: 0 = OK, the collector can operate and 1 = FAILED, the collector cannot be used).

   - `X_create()` - creates the netdata charts, following the standard netdata plugin guides as described in **[[External Plugins]]** (commands `CHART` and `DIMENSION`). The return value does matter: 0 = OK, 1 = FAILED.

   - `X_update()` - collects the values for the defined charts, following the standard netdata plugin guides as described in **[[External Plugins]]** (commands `BEGIN`, `SET`, `END`). The return value also matters: 0 = OK, 1 = FAILED.

5. The following global variables are available to be set:
   - `X_update_every` - is the data collection frequency for the collector script, in seconds.

The collector script may use more functions or variables. But all of them must begin with `X_`.

The standard netdata plugin variables are also available (check **[[External Plugins]]**). 

### X_check()

The purpose of the BASH function `X_check()` is to check is the configuration of the script is working. It should also be used for detecting configuration when possible.

For example, if your collector is about monitoring a local mysql database, the `X_check()` function may attempt to connect to a local mysql database to find out if it can read the values it needs. Keep in mind that configuration should override auto-detection.

`X_check()` is run only once for the lifetime of the collector.

### X_create()

The purpose of the BASH function `X_create()` is to create the charts and dimensions using the standard netdata plugin guides (**[[External Plugins]]**).

`X_create()` will be called just once and only after `X_check()` was successful. You can however call it yourself when there is need for it (for example to add a new dimension to an existing chart).

A non-zero return value will disable the collector.

### X_update()

`X_update()` will be called repeatedly every `X_update_every` seconds, to collect new values and send them to netdata, following the netdata plugin guides (**[[External Plugins]]**).

The function will be called with one parameter: microseconds since the last update. This value should be appended to the `BEGIN` statement of every chart updated by the collector script.

A non-zero return value will disable the collector.

### Useful functions charts.d provides

Collector scripts can use the following charts.d functions:

#### require_cmd command

`require_cmd` will check if a command is available in the running system.

For example, your `X_check()` function may use it like this:

```sh
mysql_check() {
    require_cmd mysql || return 1
    return 0
}
```

Using the above, if the command `mysql` is not available in the system, the `mysql` collector will be disabled.

#### fixid "string"

`fixid` will get a string and return a properly formatted id for a chart or dimension.

This is an expensive function that should not be used in `X_update()`. You can keep the generated id in a BASH associative array to have the values availables in `X_update()`, like this:

```sh
declare -A X_ids=()
X_create() {
   local name="a very bad name for id"

   X_ids[$name]="$(fixid "$name")"
}

X_update() {
   local microseconds="$1"

   ...
   local name="a very bad name for id"
   ...

   echo "BEGIN ${X_ids[$name]} $microseconds"
   ...
}
```

### Debugging your collectors

You can run `charts.d.plugin` by hand with something like this:

```sh

/usr/libexec/netdata/plugins/charts.d.plugin debug 1 X Y Z
```

Charts.d will run in `debug` mode, with an update frequency of `1`, evaluating only the collector scripts `X`, `Y` and `Z`. You can define zero or more collector scripts. If none is defined, charts.d will evaluate all collector script available.

