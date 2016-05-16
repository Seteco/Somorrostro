# Performance monitoring, scaled properly

"Properly"?

What is "properly"?

We know software solutions can **scale up** (i.e. you replace its resources with bigger ones), or **scale out** (i.e. you add more smaller resources to it). In both cases, to get more of it, you need to supply **more resources**.

So, what is "scaled properly"?

Traditionally, monitoring solutions centralize all metric data to provide unified dashboards across all servers. So, you install agents on all your servers to collect system and application metrics which are then sent to a central place for storage and processing. Depending on the solution you use, the central place can either **scale up** or **scale out** (or a mix of the two).

"Scaled properly" is something completely different. "Scaled properly" minimizes the need for a "central place", so that **there is nothing to be scaled**!

Wait a moment! You cannot take out the "central place" of a monitoring solution!

Yes, I can! Well... most of it, but before explaining how, let's see what happens today:

Monitoring solutions are a key component for any online service. These solutions usually consume considerable amount of resources. This is true for both "scale-up" and "scale-out" solutions. These resources require maintenance and administration too. To balance the resources required, these monitoring solutions follow a few simple rules:

1. The number of metrics collected per server is limited. They collect CPU, RAM, DISK, NETWORK metrics and a few application metrics.

2. The data collection frequency of each metric is also very low, at best it is once every 10 or 15 seconds, at worst every 5 or 10 mins.

Due to all the above, most centralized monitoring solutions are usually good for alarms and **statistics of past performance**. The alarms usually trigger every 1 to 5 minutes and you get a few low-resolution charts about the past performance of your servers.

Well... there is something wrong in this approach! Can you see it?

Let's check the netdata approach:

1. Data collection happens **per second**.

2. **Thousands of metrics** per server and application are collected, **every single second**.
 
3. Data do not leave the server they are collected.

   > Ok, I hear a few of you complaining already - you will find out... patience...

4. netdata does not use any DISK I/O while running (apart its log files - and even these can be disabled) and netdata runs with the lowest possible process priority, so that **your applications will never be affected by it**.

5. Each netdata is standalone. Your web browser connects directly to each server to present real-time dashboards. The charts are so snappy, so real-time, so fast that allow me to call netdata, **a console killer for performance monitoring**.

6. netdata is very efficient: just 2% of a single core is required and some RAM, and you can actually control how much of both you want to allocate to it.

7. netdata dashboards can be multi-server (check: [http://my-netdata.io](http://my-netdata.io)) - your browser connects to each the netdata server directly.

So, using netdata, your monitoring infrastructure is embedded on each server, limiting significantly the need of additional resources. netdata is very resource efficient and utilizes server resources that already exist and are spare (on each server).

Of course, there are a few issues that need to be addressed with this approach:

1. The database 
