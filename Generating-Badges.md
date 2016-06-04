# Netdata badges

Badges are cool!

Netdata can generate badges for any chart and any dimension. The generated badges are `SVG` and can be put onto any page with an HTML `<IMG>` reference.

Examples (using the [netdata registry](https://github.com/firehol/netdata/wiki/mynetdata-menu-item)):

- average netdata requests per second during the last minute: <a href=""><img src="https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&points=1&after=-60&value_color=grey:null%7Cgreen&label=netdata%20web%20server%20last%20min&units=%5Cs"></img></a>

- active nginx connections now <a href=""><img src="https://registry.my-netdata.io/api/v1/badge.svg?chart=nginx.connections&dimensions=active&points=1&after=-1&value_color=grey:null%7Cgreen&label=ngnix%20active%20connections&units=null"></img></a>

- cpu usage of user `root` now (100% = 1 core) <a href=""><img src="https://registry.my-netdata.io/api/v1/badge.svg?chart=users.cpu&dimensions=root&points=1&after=-1&value_color=grey:null%7Cgreen%3C10%7Cyellow%3C20%7Corange%3C50%7Cblue%3C100%7Cred&label=root%20user%20cpu&units=%25"></img></a>. This will be `green <10%`, `yellow <20%`, `orange < 50%`, `red` otherwise.

Badges do not auto-refresh. If you want to refresh a badge, you will have to do it via javascript.