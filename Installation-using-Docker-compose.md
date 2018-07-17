You can use use the following docker-compose.yml file to run netdata with docker.

Designed to be used together with [letsencrypt-nginx-proxy-companion](https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion) by Evert Ramos

Replace the Domains and Email-Adress for Letsencrypt before starting.

## Prerequisites
* [Docker](https://docs.docker.com/install/#server)
* [Docker Compose](https://docs.docker.com/compose/install/)
* [letsencrypt-nginx-proxy-companion](https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion)
* Domain configured in DNS pointing to host.

## docker-compose.yml
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

## Restrict access with basic auth
The [letsencrypt-nginx-proxy-companion](https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion) comes with **automatic basic auth support.**
just add a htpasswd file at `./data/htpasswd/netdata.example.com` in the _webproxy_ project!
https://github.com/jwilder/nginx-proxy#basic-authentication-support

