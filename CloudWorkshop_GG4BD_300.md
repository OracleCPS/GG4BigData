# Lab 300 -  Using BigData Components
(Hadoop, Hive, Pig, Spark, Oracle R)
![](images/100/image100_0.png)


## Before You Begin

### Introduction
Provision Golden Gate for Big Data simplifies your data integration by working with on-premises and cloud data source/targetss and accepting data in any shape or format. This lab will guide you on how to provision an instance of DIPC


### Objectives
- Provision Provision Golden Gate for Big Data 

### Time to Complete
Approximately 30 minutes

### What Do You Need?
Your will need:
- Oracle Cloud URL
- Oracle Cloud User and Password

### Section 1 - HIVE
This section describes basic administration Commands for HIVE. Apache Hive is a data warehouse system for data summarization, analysis and querying of large data systems in open source Hadoop platform. Hive is a data warehousing package/infrastructure built on top of Hadoop.

1. Login to Hive Console:

From the Root Console, give the following command :
sudo su - hive

It will give you the details about hive-log4j properties location and will get into hive shell. Please refer the screenshot below:

![](images/300/1.JPG)

2. Database Information:

To list out the databases in HIVE warehouse, enter the following command :
hive> show databases;

To Create a new database, enter the following command :
hive> Create database test;

![](images/300/2.JPG)

3. 