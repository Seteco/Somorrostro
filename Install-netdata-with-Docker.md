## Limitations

Running netdata in a container can limit its capabilities. Some data is not accessible or not as detailed as when running netdata on the host.

## Run netdata with docker command

Quickly start netdata with the docker command line.
Netdata is then available at http://host:19999

This is good for an internal network or to quickly analyse a host.

For a permanent installation on a public server, you should [[secure the netdata instance|netdata-security]]. See below for an example of how to install netdata with an SSL reverse proxy and basic authentication.

```bash
docker run -d --name=netdata \
  -p 19999:19999
  -v /proc:/host/proc:ro \
  -v /sys:/host/sys:ro \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  --cap-add SYS_PTRACE \
  firehol/netdata
```

above can be converted to docker-compose file for ease of management:

```yaml
version: '3'
services:
  netdata:
    image: firehol/netdata
    hostname: example.com # set to fqdn of host
    ports:
      - 19999:19999
    cap_add:
      - SYS_PTRACE
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
```

## Install Netdata using Docker Compose with SSL/TLS enabled http proxy

You can use use the following docker-compose.yml file to run netdata with docker.

Designed to be used together with [letsencrypt-nginx-proxy-companion](https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion) by Evert Ramos

Replace the Domains and Email-Adress for Letsencrypt before starting.

### Prerequisites
* [Docker](https://docs.docker.com/install/#server)
* [Docker Compose](https://docs.docker.com/compose/install/)
* [letsencrypt-nginx-proxy-companion](https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion)
* Domain configured in DNS pointing to host.

start with `docker-compose up -d` (after starting proxy!)

### docker-compose.yml
```yaml
# docker-compose.yml
# netdata in docker container with access to host network (for network metrics).
# for use with letsencrypt-nginx-proxy-companion by Evert Ramos
# https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion
# uses socat to proxy netdata to reverse proxy
# a separate network is defined to bind netdata to a fix ip address
# this ip address is used for the socat target
#
version: '3'
services:
  netdata:
    image: firehol/netdata
    hostname: example.com # set to fqdn of host
    cap_add:
      - SYS_PTRACE
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
      #- ./netdata.conf:/etc/netdata.conf:ro
    security_opt:
      - apparmor=unconfined
      - seccomp=unconfined
    logging:
      driver: "json-file"
      options:
        max-size: "50m"
        max-file: "1"
    network_mode: host
    command:  /usr/sbin/netdata -D -s /host -p 19999 -i 192.168.88.1

  socat:
    image: alpine/socat:latest
    environment:
      - VIRTUAL_HOST=netdata.example.com
      - LETSENCRYPT_HOST=netdata.example.com
      - LETSENCRYPT_EMAIL=your_email@domain.com
      - VIRTUAL_PORT=19999
    entrypoint: socat TCP-LISTEN:19999,nodelay,fork,reuseaddr TCP:192.168.88.1:19999
    expose:
      - 19999
    networks:
      - webproxy
      - netdata

networks:
  webproxy:
    external:
      name: webproxy
  netdata:
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: 192.168.88.0/24
```

### Restrict access with basic auth

[letsencrypt-nginx-proxy-companion](https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion) comes with **automatic basic auth support.**

Just add a htpasswd file at `./data/htpasswd/netdata.example.com` in the _webproxy_ project!
https://github.com/jwilder/nginx-proxy#basic-authentication-support

