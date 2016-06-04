# Netdata badges

Badges are cool!

Netdata can generate badges for any chart and any dimension. The generated badges are `SVG` and can be put onto any page with an HTML `<IMG>` reference.

Check this: the [netdata registry](https://github.com/firehol/netdata/wiki/mynetdata-menu-item) average requests per second during the last minute <a href=""><img src="https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&points=1&after=-60&value_color=grey:null%7Cgreen&label=netdata%20web%20server%20last%20min&units=%5Cs"></img></a>

Badges do not auto-refresh. If you want to refresh a badge, you will have to do it via javascript.

