# netdata via Caddy

To run netdata via Caddy's proxying, set your Caddyfile up like this:

```
netdata.domain.tld {
    proxy / localhost:19999
}
```

Other directives can be added between the curly brackets as needed.

It's easiest to set netdata up as a subdomain rather than as a subdirectory because netdata uses a lot of relative URLs.