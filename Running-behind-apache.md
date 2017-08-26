# netdata via apache's mod_proxy

## Requirements

With elevated privileges:

```sh
apt-get install libapache2-mod-proxy-html
a2enmod proxy
a2enmod proxy_http
```

## Virtual Hosts

To pass netdata through mod_proxy, you need to create a VirtualHost.  

### HTTP VirtualHost

Create a VirtualHost:

```sh
nano /etc/apache2/sites-available/netdata.conf
```

This VirtualHost  will allow you to access netdata with `http://you-public-ip/netdata/` or `http://your-domain.tld/netdata/`

```
<VirtualHost *:80>
	RewriteEngine On
	ProxyRequests Off

	# Local netdata server accessed with '/netdata/', at 127.0.0.1:19999
	ProxyPass "/netdata/" "http://127.0.0.1:19999/" connectiontimeout=5 timeout=30
	ProxyPassReverse "/netdata/" "http://127.0.0.1:19999/"

        # if the user did not give the trailing /, add it
	RewriteRule ^/netdata$ http://%{HTTP_HOST}/netdata/ [L,R=301]

	ErrorLog ${APACHE_LOG_DIR}/netdata-error.log
	CustomLog ${APACHE_LOG_DIR}/netdata-access.log combined
</VirtualHost>
```

Note: The `RewriteRule` statements makes sure that the request has a trailing `/`. Without a trailing slash, the browser will be requesting wrong URLs.

Enable the VirtualHost: 
```
a2ensite netdata.conf && service apache2 reload
```

### HTTPS VirtualHost

If mod_ssl is enabled and you're hosting at least one domain with a valid TLS certificate, then you can easily enable SSL.

Create a VirtualHost:

```sh
nano /etc/apache2/sites-available/netdata-ssl.conf
```

This VirtualHost will allow you to access netdata `https://your-domain.tld/netdata/`:  

```
<IfModule mod_ssl.c>
<VirtualHost *:443>
	RewriteEngine On
	ProxyRequests Off

	# Local 127.0.0.1:19999 netdata server accessed with '/netdata/'
	ProxyPass "/netdata/" "http://127.0.0.1:19999/" connectiontimeout=5 timeout=30
	ProxyPassReverse "/netdata/" "http://127.0.0.1:19999/"

        # if the user did not give the trailing /, add it
	RewriteRule ^/netdata$ https://%{HTTP_HOST}/netdata/ [L,R=301]

	ErrorLog ${APACHE_LOG_DIR}/netdata-error.log
	CustomLog ${APACHE_LOG_DIR}/netdata-access.log combined
</VirtualHost>
</IfModule>
```

Enable the VirtualHost: 
```
a2ensite netdata-ssl.conf && service apache2 reload
```

## Dynamic proxy any netdata through an apache

You can proxy multiple netdata via a single apache server, with URLs like `http://your.apache/netdata/NETDATA_SERVER/`, where `NETDATA_SERVER` is any netdata server on your network.

```
        ProxyPassMatch "^/netdata/([A-Za-z0-9\._-]+)/(.*)" "http://$1:19999/$2" connectiontimeout=5 timeout=30
```

> IMPORTANT: This allows your apache users to connect to port 19999 on any server on your network.

Changes are applied by reloading or restarting apache.

## Enable Basic Auth

If you wish to add an authentication (user/password) to access your netdata

Then with elevated privileges:  

1) Install required dependencies  
`apt-get install apache2-utils`

2) Generate password for user 'netdata'  
`htpasswd -c /etc/apache2/.htpasswd netdata`

3) Add to virtualhost:

```
	<Proxy *>
		Order deny,allow
		Allow from all
	</Proxy>

<Location /netdata>
	AuthType Basic
	AuthName "Protected site"
	AuthUserFile /etc/apache2/.htpasswd
	Require valid-user
	Order deny,allow
	Allow from all
</Location>
```

Which leads you to something like: 

```
<VirtualHost *:80>
	RewriteEngine On
	ProxyRequests Off
	<Proxy *>
		Order deny,allow
		Allow from all
	</Proxy>

	# Local 127.0.0.1:19999 netdata server accessed with '/netdata/'
	ProxyPass "/netdata/" "http://127.0.0.1:19999/" connectiontimeout=5 timeout=30
	ProxyPassReverse "/netdata/" "http://127.0.0.1:19999/"

        # if the user did not give the trailing /, add it
	RewriteRule ^/netdata$ http://%{HTTP_HOST}/netdata/ [L,R=301]

	<Location /netdata/>
		AuthType Basic
		AuthName "Protected site"
		AuthUserFile /etc/apache2/.htpasswd
		Require valid-user
		Order deny,allow
		Allow from all
	</Location>

	ErrorLog ${APACHE_LOG_DIR}/netdata-error.log
	CustomLog ${APACHE_LOG_DIR}/netdata-access.log combined

</VirtualHost>
```

Note: Changes are applied by reloading or restarting Apache.

# Netdata configuration

You might edit netdata to suit those changes.

`nano /etc/netdata/netdata.conf`

Then restart netdata to apply the new configuration.
```sh
killall netdata
/usr/sbin/netdata
```


## Response compression

If you plan to use netdata exclusively via apache, you can gain some performance by preventing double compression of its output (netdata compresses its response, apache re-compresses it) by editing `/etc/netdata/netdata.conf` and setting:

```
enable web responses gzip compression = no
```

Once you disable compression at netdata (and restart it), please verify you receive compressed responses from apache (it is important to receive compressed responses - the charts will be more snappy).

## Limit direct access to netdata

You would also need to instruct netdata to listen only from localhost `127.0.0.1` or `::1`.

```
bind to = 127.0.0.1
```
or  
```
bind to = ::1
```

## prevent the double access.log

apache logs accesses and netdata logs them too. You can prevent netdata from generating its access log, by setting this in `/etc/netdata/netdata.conf`:

```
      access log = none
```

