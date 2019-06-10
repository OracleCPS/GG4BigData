# Lab 300 -  Using BigData Components
(Hadoop, Hive, Pig, Spark, Oracle R)
![](images/100/image100_0.png)


## Before You Begin

### Introduction
Provision Golden Gate for Big Data simplifies your data integration by working with on-premises and cloud data source/targetss and accepting data in any shape or format. This lab will give you basic understanding about various Big data Products.

### Objectives
- Overview about different Golden Gate big data Products. 

### Time to Complete
Approximately 30 minutes

### What Do You Need?
Your will need:
- Access to Big Data Enviornment using Putty

### Section 1 - HIVE
This section describes basic administration Commands for HIVE. Apache Hive is a data warehouse system for data summarization, analysis and querying of large data systems in open source Hadoop platform. Hive is a data warehousing package/infrastructure built on top of Hadoop.

### 1. Login to Hive Console:

From the Root Console, give the following command :
sudo su - hive

It will give you the details about hive-log4j properties location and will get into hive shell. Please refer the screenshot below:

![](images/300/1.JPG)

### 2. Database Information:

To list out the databases in HIVE warehouse, enter the following command :
hive> show databases;

To Create a new database, enter the following command :
hive> Create database test;

![](images/300/2.JPG)

### 3. Table Creation & Deletion:

In order to create a new table in a specific database, you first need to navigate to that database, Use the below commands:
hive>use test;

hive>create table test1(id INT, Name String) row format delimited fields terminated by ',' stored as textfile;

hive> describe test1;

![](images/300/3.JPG)

In order to drop table, just use the following simple command:
hive> show tables;

hive> drop table if exists test1;

hive> Show tables;

When you executre show tables first time, you will see test1 listed. Once you drop it and list tables again, it will not be their. Please refer the screenshot below:

![](images/300/5.JPG)

### 4. Schemas and Databases:

In hove, Schemas and Databases resolve to the same thing, you can either use Schemas or databases, Use the below commands:
hive> Show schemas;

Now we will drop a database name test

hive> drop database if exists test;

hive> show databases;

Please refer the below screenshot:

![](images/300/6.JPG)

### 5. Version Details:

You can check the details of Hive version with a very simple command. This command has to be executed outside hive shell.

] hive --version
This command will give you Hive version as well as subversion, Please refer to below screenshot.

![](images/300/4.JPG)


### SECTION 2 - PIG 

Pig is a high level scripting language that is used with Apache Hadoop. Pig enables data workers to write complex data transformations without knowing Java. Apache Pig is a platform for analyzing large data sets that consists of a high-level language for expressing data analysis programs, coupled with infrastructure for evaluating these programs

### 1. Login to Pig Console:

From the Root Console, Just type 'Pig' and you will get into grunt console.

Root] pig

It will list down various classes under Pig. Please refer the screenshot below.

![](images/300/pig_2.JPG)

### 2. Pig Version:

You can see the details of the Version of Pig as you go into the grunt console. It also has a dedicated command to check it

Root] pig -version

Please refer the screenshot below:

![](images/300/pig_1.JPG)

### 3. Data Load:

You can load data from any file to pig relation using the LOAD Command. File can be loaded from hdfs, depending upon the Pig Start up mode.

example : emp= load 'user/file1' as (id:int,name:chararray,sal:int,sex:chararry,dno:int);

You can also list the previously used commands by using History command. Please see below

![](images/300/pig_3.JPG)

In this example, Data from customer.txt and orders.txt is getting loaded into Customers and Orders respectively.

### 4. Script Execution:

You can execute any pig commands by putting them in a .pig file and running the file at Pig shell. Please see the command below:

grunt> run sample_script.pig

In this example, sample_script contains a LOAD command which is loading into an available pig relation. Please refer the screenshot below:

![](images/300/pig_4.JPG)

### 5. Listing files:

You can simply list the available files under a file system in the grunt console, Please see the command below:

grunt>sh ls

In  this example, there are some log files created after runnung sample_script.pig, Please refer the screenshot below:

![](images/300/pig_5.JPG)

Just Use the quit command to come out of the grunt shell.

grunt> quit

![](images/300/pig_6.JPG)

