# Netdata badges

Badges are cool!

Netdata can generate badges for any chart and any dimension. The generated badges are `SVG` and can be put onto any page with an HTML `<IMG>` reference.

Examples (using the [netdata registry](https://github.com/firehol/netdata/wiki/mynetdata-menu-item)):

- average netdata requests per second during the last complete minute (00-59, it updates its value once per minute):

  <a href="https://registry.my-netdata.io/#netdata_netdata"><img src="https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&points=1&after=-60&value_color=grey:null%7Cgreen&label=netdata%20web%20server%20last%20min&units=%5Cs"></img></a>

- active nginx connections now

  <a href="https://registry.my-netdata.io/#nginx_nginx"><img src="https://registry.my-netdata.io/api/v1/badge.svg?chart=nginx.connections&dimensions=active&points=1&after=-1&value_color=grey:null%7Cgreen&label=ngnix%20active%20connections&units=null"></img></a>

- cpu usage of user `root` now (100% = 1 core). This will be `green <10%`, `yellow <20%`, `orange <50%`, `blue <100%` (1 core), `red` otherwise (you define thresholds and colors on the URL).

  <a href="https://registry.my-netdata.io/#apps_cpu" target="_blank"><img src="https://registry.my-netdata.io/api/v1/badge.svg?chart=users.cpu&dimensions=root&points=1&after=-1&value_color=grey:null%7Cgreen%3C10%7Cyellow%3C20%7Corange%3C50%7Cblue%3C100%7Cred&label=root%20user%20cpu&units=%25"></img></a>


## How to create badges

TODO

## FAQ

#### Can badges auto-refresh?
No, not currently. If you want to refresh a badge, you will have to do it via javascript (same logic as updating any other image).

#### Is it fast?
On modern hardware, netdata can generate about **2.000 badges per second per core**, before noticing any delays. It generates a badge in about half a millisecond!
