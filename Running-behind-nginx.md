# netdata via nginx

To pass netdata via a nginx, use this:

```
upstream backend {
    # the netdata server
    server 127.0.0.1:19999;
    keepalive 64;
}

server {
    # nginx listens to this
    listen 10.1.1.1:80;

    # the virtual host name of this
    server_name netdata.example.com;

    location / {
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Server $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://backend;
        proxy_http_version 1.1;
        proxy_pass_request_headers on;
        proxy_set_header Connection "keep-alive";
        proxy_store off;
    }
}
```

