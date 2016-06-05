# Netdata badges

Badges are cool!

Netdata can generate badges for any chart, any dimension at any timeframe.

The generated badges are `SVG` and can be put onto any page with an HTML `<IMG>` reference.

Examples (using the [netdata registry](https://github.com/firehol/netdata/wiki/mynetdata-menu-item) for serving them):

- average netdata requests per second during the last complete minute (XX:XX:00 - XX:XX:59, it updates its value once per minute):

  <a href="https://registry.my-netdata.io/#netdata_netdata"><img src="https://registry.my-netdata.io/api/v1/badge.svg?chart=netdata.requests&dimensions=requests&after=-60&value_color=grey:null%7Cgreen&label=netdata%20web%20server%20last%20min&units=%5Cs"></img></a>

- active nginx connections now

  <a href="https://registry.my-netdata.io/#nginx_nginx"><img src="https://registry.my-netdata.io/api/v1/badge.svg?chart=nginx.connections&dimensions=active&value_color=grey:null%7Cgreen&label=ngnix%20active%20connections&units=null"></img></a>

- cpu usage of user `root` now (100% = 1 core). This will be `green <10%`, `yellow <20%`, `orange <50%`, `blue <100%` (1 core), `red` otherwise (you define thresholds and colors on the URL).

  <a href="https://registry.my-netdata.io/#apps_cpu"><img src="https://registry.my-netdata.io/api/v1/badge.svg?chart=users.cpu&dimensions=root&value_color=grey:null%7Cgreen%3C10%7Cyellow%3C20%7Corange%3C50%7Cblue%3C100%7Cred&label=root%20user%20cpu%20now&units=%25"></img></a>

- mysql queries per second now

  <a href="https://registry.my-netdata.io/#mysql_local"><img src="https://registry.my-netdata.io/api/v1/badge.svg?chart=mysql_local.queries&dimensions=questions&label=mysql%20queries%20now&value_color=blue&units=%5Cs"></img></a>

- bind (ISC named) max DNS queries per second during the last complete hour (XX:00:00 - XX:59:59, updates its value once per hour)

  <a href="https://registry.my-netdata.io/#named_local"><img src="https://registry.my-netdata.io/api/v1/badge.svg?chart=named_local.received_requests&after=-3600&method=max&value_color=orange&label=bind%20max%20hourly%20requests&units=%5Cs"></img></a>

---

## How to create badges

The basic URL is `http://your.netdata:19999/api/v1/badge.svg?option1&option2&option3&...`.

Here is what you can put for `options` (these are standard netdata API options):

- `chart=CHART.NAME`

  The chart to get the values from

- `dimensions=DIMENSION1|DIMENSION2|...`

  The dimensions of the chart to use. If you don't set any dimension, all will be used. When multiple dimensions are used, netdata will sum their values. You can append `options=absolute` if you want this sum to convert all values to positive before adding them.

- `before=SECONDS` and `after=SECONDS`

  The timeframe. These can be absolute unix timestamps, or relative to now, number of seconds. By default `before=0` and `after=-1` (1 second in the past).

  To get the last minute set `after=-60`. This will give the average of the last complete minute (XX:XX:00 - XX:XX:59).

  To get the max of the last hour set `after=-3600&method=max`. This will give the maximum value of the last complete hour (XX:00:00 - XX:59:59)

- `method=max` or `method=average` (the default)

  If netdata will have to reduce (aggregate) the data to calculate the value, which aggregation method to use.

- `options=opt1|opt2|opt3|...`

  These fine tune various options of the API. Here is what you can use for badges (the API has more option, but only these are useful for badges):

  - `percentage`, instead of returning the value, calculate the percentage of the sum of the selected dimensions, versus the sum of all the dimensions of the chart. This also sets the units to `%`.

  - `absolute` or `abs`, turn all values positive and then sum them.

  - `min2max`, when reducing data, calculate the difference `max value - min value`.

  - `flip`, use the earliest value of the timeframe, not the latest.

  - `null2zero`, report null values as zero.

These are options dedicated to badges:

- `label=TEXT`

  The label of the badge.

- `units=TEXT`

  The units of the badge. If you want to put a `/`, please put a `\`. This is because netdata allows badges parameters to be given as path in URL, instead of query string. You can also use `null` or `empty` to show it without any units.

- `multiply=NUMBER`

  Multiply the value with this number. The default is `1`.

- `divide=NUMBER`

  Divide the value with this number. The default is `1`.

- `label_color=COLOR`

  The color of the label (the left part). You can use any HTML color, include `#NNN` and `#NNNNNN`. The following colors are defined in netdata (and you can use them by name): `green`, `brightgreen`, `yellow`, `yellowgreen`, `orange`, `red`, `blue`, `grey`, `gray`, `lightgrey`, `lightgray`. These are taken from https://github.com/badges/shields so they are compatible with standard badges.

- `value_color=COLOR:null|COLOR<VALUE|COLOR>VALUE|COLOR>=VALUE|COLOR<=VALUE|...`

  You can add a pipe delimited list of conditions to pick the color. The first matching (left to right) will be used.

  Example: `value_color=grey:null|green<10|yellow<100|orange<1000|blue<10000|red`

  The above will set `grey` if no value exists (not collected within the `gap when lost iterations above` in netdata.conf for the chart), `green` if the value is less than 10, `yellow` if the value is less than 100, etc up to `red` which will be used if no other conditions match.

  The supported operators are `<`, `>`, `<=`, `>=`, `=`.

---

## Escaping URLs

Keep in mind that if you add badge URLs to your HTML pages you have to escape the special characters:

character|name|escape sequence
:-------:|:--:|:-------------:
` `|space (in labels and units)|`%20`
`#`|hash (for colors)|`%23`
`%`|percent (in units)|`%25`
`<`|less than|`%3C`
`>`|greater than|`%3E`
`\`|backslash (when you need a `/`)|`%5C`
`|`|pipe (delimiting parameters)|`%7C`

---

## Using the path instead of the query string

The badges can also be generated using the URL path for passing parameters. The format is exactly the same.

So instead of:

  `http://your.netdata:19999/api/v1/badge.svg?option1&option2&option3&...`

you can write:

  `http://your.netdata:19999/api/v1/badge.svg/option1/option2/option3/...`

You can also append anything else you like, like this:

  `http://your.netdata:19999/api/v1/badge.svg/option1/option2/option3/my-super-badge.svg`

## FAQ

#### Can badges auto-refresh?
No, not currently. If you want to refresh a badge, you will have to do it via javascript (same logic as updating any other image).

#### Is it fast?
On modern hardware, netdata can generate about **2.000 badges per second per core**, before noticing any delays. It generates a badge in about half a millisecond!

#### Embedding badges in github
You cannot add SVG images with markdown. You have to give them as HTML (directly in .md files).

For example, this is the cpu badge shown above:

```html
<a href="https://registry.my-netdata.io/#apps_cpu">
    <img src="https://registry.my-netdata.io/api/v1/badge.svg?chart=users.cpu&dimensions=root&points=1&after=-1&value_color=grey:null%7Cgreen%3C10%7Cyellow%3C20%7Corange%3C50%7Cblue%3C100%7Cred&label=root%20user%20cpu%20now&units=%25"></img>
</a>
```

which produces this: <a href="https://registry.my-netdata.io/#apps_cpu">
    <img src="https://registry.my-netdata.io/api/v1/badge.svg?chart=users.cpu&dimensions=root&value_color=grey:null%7Cgreen%3C10%7Cyellow%3C20%7Corange%3C50%7Cblue%3C100%7Cred&label=root%20user%20cpu%20now&units=%25"></img>
</a>
