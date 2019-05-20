# Lab 1000 -  MySQL to Cassandra
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

## Replicat from trail files on the target machine to Kafka topic.

1. We already have a trail file created in the GGBD home. We will be using the same trail file to replicate to OCI Object Storage.

![](images/500/image100_1.pn)

Please refer to Lab 400 for more information.

2. Create the csndra.props file in dirprm folder in your Golden Gate installation folder.
```
[oracle@gg4bd-target01 ggbd_home1]$ cd dirprm
[oracle@gg4bd-target01 dirprm]$ vi csndra.props
```
Copy paste the below text in kafka.props.

```
#RCSNDRA Properties for Cassandra
#
gg.handlerlist=cassandra
#The handler properties
gg.handler.cassandra.type=cassandra
gg.handler.cassandra.mode=op
gg.handler.cassandra.contactPoints=localhost
gg.handler.cassandra.ddlHandling=CREATE,ADD,DROP
gg.handler.cassandra.compressedUpdates=false
gg.handler.cassandra.cassandraMode=async
gg.handler.cassandra.consistencyLevel=ONE
goldengate.userexit.timestamp=utc
goldengate.userexit.writers=javawriter
javawriter.stats.display=TRUE
javawriter.stats.full=TRUE
##
gg.log=log4j
gg.log.level=INFO
###      
gg.report.time=30sec
###      
gg.classpath=dirprm/:/u01/app/cassandra/lib/*:/u01/app/cassandra/lib/cassandra-javadriver/*:/u01/app/cassandra/lib/cassandra-javadriver/lib/*
##

```
Save the text using wq!

3. Edit the replicat with the below commands.
```
GGSCI (gg4bd-target01) 1> edit param RKAFKA
```
Add the below content into RKAFKA.
```
REPLICAT RCSNDRA
TARGETDB LIBFILE libggjava.so SET property=dirprm/csndra.props
REPORTCOUNT EVERY 1 MINUTES, RATE
GROUPTRANSOPS 1000
MAP employees.*, TARGET employees.*;

```

4. Add the replicat with the below command.

```
add replicat RCSNDRA, exttrail ./dirdat/eb

```
