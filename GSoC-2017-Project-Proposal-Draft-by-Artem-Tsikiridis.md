**Name:** Artem Tsikiridis

**Emails:** tsikiridis15@aueb.gr, tsekis79@gmail.com

**Synopsis**

netdata is an open-source scalable, distributed, real-time, performance and health monitoring solution for Linux, FreeBSD and MacOS. One of the main goals of netdata is to offer insights on why systems and applications have performance issues. This is achieved by presenting to the system administrator a multitude of performance metrics of the underlying systems in a meaningful, visual way.

Apache Hadoop is a prominent Big Data system and one of the first implementations of the MapReduce programming paradigm. Hadoop has inspired an entire ecosystem of software and many companies and research institutions have in-house clusters or clusters living in the cloud with dedicated system administrators monitoring their well-being.


My GSoC 2017 project proposal for netdata is to implement 1) system metrics collectors for Hadoop, Spark and Flink so that netdata users can exploit the interactive web dashboards of netdata and 2) health alarms informing the user of discrepancies. Finally, all features should be accompanied by Wiki guides on their usage. 


**How would the community benefit from this?**

1. Bob is a system administrator and an avid netdata user. Recently, he has been asked to monitor a moderate Hadoop Cluster used by the company's data scientists. Bob is not satisfied by the static web interface monitoring the status of Hadoop and would love to have for this task the netdata charts and dashboards he is accustomed to. Moreover, he would very much love to receive his Hadoop alarms on slack. After browsing the netdata repository (autumn 2017), he realises there are now data collectors and  health alarms for Hadoop. Bob is relieved that he can keep using netdata for this task.

2. Alice is a data scientist working at a company that uses netdata for system monitoring. She frequently deploys Flink jobs on the company's cluster. However, she notices that in the last few days jobs are taking longer to complete. She informs Bob, the company's system administrator (by opening a ticket, of course!). Bob can handle it immediately as he accesses the netdata web interface for the cluster and sees that there are several blacklisted nodes (network issues?). Bob handles the issue and introduces an alarm for this situation.

**About me**

- I am finishing my MSc in Computer Science at Athens University of Economics and Business, Greece.
- I have successfully completed a Google Summer of Code in 2014 at Apache Flink. Here is a blogpost of the result: https://flink.apache.org/news/2014/11/18/hadoop-compatibility.html
- I have interned twice at Cern Geneva as a software engineering intern. I am more confident with Java and Python and I am familiar with the Hadoop ecosystem.
- My github profile: github.com/atsikiridis 
- My stackoverflow profile: http://stackoverflow.com/users/2568511/artem-tsikiridis

**Deliverables**


*4 May - 30 May (Community bonding period)*

1. Understand what it takes to develop data collectors for netdata along with relevant health alarms. (week 1)

2. Contribute to the community by fixing a starter issue to make sure the workflow of contribution is clear. (week 2)

3. Write a document describing all metrics we need to cover for Hadoop, Flink and Spark along with scenarios that consist as alarms. (week 3 and 4)


*30 May - 30 June (Phase 1 - Hadoop)*

1) Take note of the API calls that need to be made. Hadoop already has a static web interface (how are these metrics obtained?) and exposes its cluster statistics via JMX. We may also use pure Java to interface with the cluster. Translate the document of the bonding period to specific API calls. (week 1)

2) Implement Hadoop data collector (week 2 and 3).

3) Implement alarm scenarios and charts configurations and a Wiki page. (week 4)

*30 June - 28 July (Phase 2 - Spark)*

Follow same steps as in phase one for Apache Spark.

*29 July - 21 August (Phase 3 - Flink)*

Follow same steps as in phase one for Apache Flink.

*22 August - 29 August (Final Week)* 
1) Work on prevailing issues (regression tests), add more documentation and prepare for code submission to Google.


*Acknowledgement*

This proposal has been shaped with the guidance of Costa Tsaousis. Thank you!


