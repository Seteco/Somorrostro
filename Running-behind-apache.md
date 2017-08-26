# netdata via apache's mod_proxy

Below you can find instructions for configuring an apache server for:

1. proxy a single netdata via an HTTP and HTTPS virtual host
2. dynamically proxy any number of netdata
3. add user authentication
4. adjust netdata settings to get optimal results


## Requirements

With elevated privileges:

```sh
apt-get install libapache2-mod-proxy-html
a2enmod proxy
a2enmod proxy_http
```

## Virtual Hosts

You can proxy netdata through apache, using a dedicated netdata virtual host.

### HTTP VirtualHost

Create a VirtualHost:

```sh
nano /etc/apache2/sites-available/netdata.conf
```

This VirtualHost  will allow you to access netdata with `http://your-public-ip/netdata/` or `http://your-domain.tld/netdata/`

```
<VirtualHost *:80>
	RewriteEngine On
	ProxyRequests Off
	ProxyPreserveHost On

	<Proxy *>
		Require all granted
	</Proxy>

	# Local netdata server accessed with '/netdata/', at 127.0.0.1:19999
	ProxyPass "/netdata/" "http://127.0.0.1:19999/" connectiontimeout=5 timeout=30 keepalive=on
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
	ProxyPreserveHost On

	<Proxy *>
		Require all granted
	</Proxy>

	# Local 127.0.0.1:19999 netdata server accessed with '/netdata/'
	ProxyPass "/netdata/" "http://127.0.0.1:19999/" connectiontimeout=5 timeout=30 keepalive=on
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

## Dynamically proxy any number of netdata through a single apache

You can proxy multiple netdata via a single apache server, with URLs like `http://your.apache/netdata/NETDATA_SERVER/`, where `NETDATA_SERVER` is any netdata server on your network.

Add the following to an existing virtual host.

```
    RewriteEngine On
    ProxyRequests Off
    ProxyPreserveHost On

    # proxy any host, on port 19999
    ProxyPassMatch "^/netdata/([A-Za-z0-9\._-]+)/(.*)" "http://$1:19999/$2" connectiontimeout=5 timeout=30 keepalive=on

    # make sure the user did not forget to add a trailing /
    RewriteRule "^/netdata/([A-Za-z0-9\._-]+)$" http://%{HTTP_HOST}/netdata/$1/ [L,R=301]
```

> IMPORTANT<br/>
> The above config allows your apache users to connect to port 19999 on any server on your network.

If you want to control the servers your users can connect to, use some like the following. This allows only `server1`, `server2`, `server3` and `server4`.

```
    RewriteEngine On
    ProxyRequests Off
    ProxyPreserveHost On

    # proxy any host, on port 19999
    ProxyPassMatch "^/netdata/(server1|server2|server3|server4)/(.*)" "http://$1:19999/$2" connectiontimeout=5 timeout=30 keepalive=on

    # make sure the user did not forget to add a trailing /
    RewriteRule "^/netdata/([A-Za-z0-9\._-]+)$" http://%{HTTP_HOST}/netdata/$1/ [L,R=301]
```

Changes are applied by reloading or restarting apache.

## Enable Basic Auth

If you wish to add an authentication (user/password) to access your netdata

Then with elevated privileges:  

1) Install required dependencies with `apt-get install apache2-utils`

2) Generate password for user 'netdata' using `htpasswd -c /etc/apache2/.htpasswd netdata`

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
	ProxyPreserveHost On

	<Proxy *>
		Order deny,allow
		Allow from all
	</Proxy>

	# Local 127.0.0.1:19999 netdata server accessed with '/netdata/'
	ProxyPass "/netdata/" "http://127.0.0.1:19999/" connectiontimeout=5 timeout=30 keepalive=on
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

You might edit `/etc/netdata/netdata.conf` to optimize your setup a bit. For applying these changes you need to restart netdata.

## Response compression

If you plan to use netdata exclusively via apache, you can gain some performance by preventing double compression of its output (netdata compresses its response, apache re-compresses it) by editing `/etc/netdata/netdata.conf` and setting:

```
[web]
    enable gzip compression = no
```

Once you disable compression at netdata (and restart it), please verify you receive compressed responses from apache (it is important to receive compressed responses - the charts will be more snappy).

## Limit direct access to netdata

You would also need to instruct netdata to listen only on `localhost`, `127.0.0.1` or `::1`.

```
[web]
    bind to = localhost
```
or  
```
[web]
    bind to = 127.0.0.1
```
or  
```
[web]
    bind to = ::1
```

## prevent the double access.log

apache logs accesses and netdata logs them too. You can prevent netdata from generating its access log, by setting this in `/etc/netdata/netdata.conf`:

```
[global]
    access log = none
```

## Troubleshooting mod_proxy

Make sure the requests reach netdata, by examinng `/var/log/netdata/access.log`.

1. if the requests do not reach netdata, your apache does not forward them.
2. if the requests reach netdata by the URLs are wrong, you have not re-written them properly.

