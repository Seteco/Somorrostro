# netdata via apache's mod_proxy

To pass netdata through mod_proxy, use a config like this:

```
<VirtualHost *:80>
        RewriteEngine On
        ProxyRequests Off
        <Proxy *>
                Order deny,allow
                Allow from all
        </Proxy>

        # first netdata server called 'box', at 127.0.0.1:19999
        ProxyPass "/netdata/box/" "http://127.0.0.1:19999/" connectiontimeout=5 timeout=30
        ProxyPassReverse "/netdata/box/" "http://127.0.0.1:19999/"
        RewriteRule ^/netdata/box$ http://%{HTTP_HOST}/netdata/box/ [L,R=301]

        # second netdata server called 'pi1' at 10.11.12.81:19999
        ProxyPass "/netdata/pi1/" "http://10.11.12.81:19999/" connectiontimeout=5 timeout=30
        ProxyPassReverse "/netdata/pi1/" "http://10.11.12.81:19999/"
        RewriteRule ^/netdata/pi1$ http://%{HTTP_HOST}/netdata/pi1/ [L,R=301]

        # rest of virtual host config
```

The `RewriteRule` statements make sure the request has a trailing `/`. Without a trailing slash, the browser will be requesting wrong URLs.

## Response compression

If you plan to use netdata exclusively via apache, you can gain some performance by preventing double compression of its output (netdata compresses its response, apache re-compresses it) by editing `/etc/netdata/netdata.conf` and setting:

```
enable web responses gzip compression = no
```

Once you disable compression at netdata (and restart it), please verify you receive compressed responses from apache.

## limit direct access to netdata

You would also need to instruct netdata to listen only to `127.0.0.1` or `::1`.

To limit access to netdata only from localhost, set `bind socket to IP = 127.0.0.1` or `bind socket to IP = ::1` in `/etc/netdata/netdata.conf`.
