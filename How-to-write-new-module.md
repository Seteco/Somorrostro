Basic information
=================

Writing new python module is simple. You just need to remember to include 4 major things:
- **ORDER** global list
- **CHART** global dictionary
- **Service** class
- **_get_data** method

### Global variables `ORDER` and `CHART`

`ORDER` list should contain the order of chart ids. Example:
```py
ORDER = ['first_chart', 'second_chart', 'third_chart']
```

`CHART` dictionary is a little bit trickier. It should contain chart definition in following format:
```py
CHART = {
    id: {
        'options': [name, title, units, family, context, charttype],
        'lines': [
            [unique_dimension_name, name, algorithm, multiplier, divisor]
        ]}
```
All names are better explained in [External Plugins](https://github.com/firehol/netdata/wiki/External-Plugins) section.
Parameters like `priority` and `update_every` are handled by `python.d.plugin`.

### `Service` class

Every module needs to implement its own `Service` class. This class should inherit from one of framework classes:
- `SimpleService`
- `UrlService`
- `SocketService`
- `LogService`
- `ExecutableService`

Also it needs to invoke parent class constructor in a specific way as well as assign global variables to class variables. 

Simple example:
```py
from base import UrlService
class Service(UrlService):
    def __init__(self, configuration=None, name=None):
        UrlService.__init__(self, configuration=configuration, name=name)
        self.order = ORDER
        self.definitions = CHARTS
```

### `_get_data` collector/parser

This method should grab raw data from `_get_raw_data`, parse it, and return a dictionary where keys are unique dimension names or `None` if no data is collected.

Example:
```py
def _get_data(self):
    try:
        raw = self._get_raw_data().split(" ")
        return {'active': int(raw[2])}
    except (ValueError, AttributeError):
        return None
```

More about framework classes
============================

Every framework class has some user-configurable variables which are specific to this particular class. Those variables should have default values initialized in child class constructor.

If module needs some additional user-configurable variable, it can be accessed from `self.configuration` list and assigned in constructor or custom `check` method. Example:
```py
def __init__(self, configuration=None, name=None):
    UrlService.__init__(self, configuration=configuration, name=name)
    try:
        self.baseurl = str(self.configuration['baseurl'])
    except (KeyError, TypeError):
        self.baseurl = "http://localhost:5001"
```

Classes implement `_get_raw_data` which should be used to grab raw data. This method usually returns list of strings.

### `SimpleService`

_This is last resort class, if a new module cannot be written by using other framework class this one can be used._

_Example: `mysql`, `sensors`_

It is the lowest-level class which implements most of module logic, like:
- threading
- handling run times
- chart formatting
- logging
- chart creation and updating

### `LogService`

_Examples: `apache_cache`, `nginx_log`_

_Variable from config file_: `log_path`.

Object created from this class reads new lines from file specified in `log_path` variable. It will check if file exists and is readable. Also `_get_raw_data` returns list of strings where each string is one line from file specified in `log_path`.

### `ExecutableService`

_Examples: `exim`, `postfix`_

_Variable from config file_: `command`.

This allows to execute a shell command in a secure way. It will check for invalid characters in `command` variable and won't proceed if there is one of:
- '&'
- '|'
- ';'
- '>'
- '<'

For additional security it uses python `subprocess.Popen` (without `shell=True` option) to execute command. Command can be specified with absolute or relative name. When using relative name, it will try to find `command` in `PATH` environment variable as well as in `/sbin` and `/usr/sbin`.

`_get_raw_data` returns list of decoded lines returned by `command`.

### UrlService

_Examples: `apache`, `nginx`, `tomcat`_

_Variables from config file_: `url`, `user`, `pass`.

If data is grabbed by accessing service via HTTP protocol, this class can be used. It can handle HTTP Basic Auth when specified with `user` and `pass` credentials.

`_get_raw_data` returns list of utf-8 decoded strings (lines).

### SocketService

_Examples: `dovecot`, `redis`_

_Variables from config file_: `unix_socket`, `host`, `port`, `request`.

Object will try execute `request` using either `unix_socket` or TCP/IP socket with combination of `host` and `port`. This can access unix sockets with SOCK_STREAM or SOCK_DGRAM protocols and TCP/IP sockets in version 4 and 6 with SOCK_STREAM setting.

Sockets are accessed in non-blocking mode with 15 second timeout.

After every execution of `_get_raw_data` socket is closed, to prevent this module needs to set `_keep_alive` variable to `True` and implement custom `_check_raw_data` method.

`_check_raw_data` should take raw data and return `True` if all data is received otherwise it should return `False`. Also it should do it in fast and efficient way.