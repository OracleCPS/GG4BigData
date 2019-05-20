# Lab 900 -  Oracle to Kafka (json format) / EventHub
![](images/100/image100_0.png)


## Before You Begin
- We should have Oracle installed on the source compute instance.
- We should have Kafka installed on the target compute instance.
- We require to have golden gate binaries installed for the Oracle database on the source side.
- We require to have golden gate for Big Data binaries installed on the target side.


### Introduction
Provision Golden Gate for Kafka to simplify your data integration by working with on-premises and cloud data source/targets and accepting data in any shape or format. This labs will guide through the steps required to start a replication stream between an Oracle database and a Kafka queue.

### Objectives
- Extract from Oracle to generate the Trail Files on Source
- Dump the trail files from Source to target machine
- Replicate from trail files on the target machine to Kafka topic.

### Time to Complete
Approximately 30 minutes


### What Do You Need?
You will need:
- Putty if you are using 

## Oracle Installation on the Source Compute instance


## Kafka installation on the Target Compute Instance

This tutorial assumes you are starting fresh and have no existing Kafka or ZooKeeper data.

- Using SCP command transfer the downloaded files to the place you want in Target machine.

![](images/900/image900_1.png)

- Service Name: **{CDB_SOURCE_SERVICE_NAME}**

![](images/1300/image1300_7.JPG)    
```
where:
    {LOCAL_AGENT} - Select the local DIPC agent
    {SERVICE_NAME} - CDB Service name string for the source database server.

```

## Golden Gate for Oracle Installation on source side

## Golden Gate for Big Data Installation on target inside

## Extract from Oracle to generate the Trail Files on Source

1. Go to the directory where your ggsci is there and run it.

```
[oracle@gg4dbd-source01 ogg123]$ ./ggsci

Oracle GoldenGate Command Interpreter for Oracle
Version 12.3.0.1.4 OGGCORE_12.3.0.1.0_PLATFORMS_180415.0359_FBO
Linux, x64, 64bit (optimized), Oracle 12c on Apr 16 2018 00:53:30
Operating system character set identified as UTF-8.

Copyright (C) 1995, 2018, Oracle and/or its affiliates. All rights reserved.



GGSCI (gg4dbd-source01) 1>
```
2. Add your extract file.
```
GGSCI (gg4dbd-source01) 3> edit params E_BAP
```
Paste the below content to the file and save.

```
extract e_bap
SETENV (TNS_ADMIN="/u01/app/oracle/product/12.2.0/dbhome_1/network/admin")
SETENV (ORACLE_HOME="/u01/app/oracle/product/12.2.0/dbhome_1")
exttrail ./dirdat/bh
-- userid c##ggadmin@orcl2, password Oracle1
useridalias ggalias
statoptions resetreportstats
report at 12:00
reportrollover at 12:00
reportcount every 60 seconds, rate
table pdb1.hr.regions;
```

3. Now add the Extract using below commands.
```
 ADD EXTRACT e_bap, TRANLOG, BEGIN NOW
 ADD EXTTRAIL ./dirdat/et, EXTRACT e_bap
```

## Dump the trail files from Source to target machine

1. Go to the directory where your ggsci is there and run it.

```
[oracle@gg4dbd-source01 ogg123]$ ./ggsci

Oracle GoldenGate Command Interpreter for Oracle
Version 12.3.0.1.4 OGGCORE_12.3.0.1.0_PLATFORMS_180415.0359_FBO
Linux, x64, 64bit (optimized), Oracle 12c on Apr 16 2018 00:53:30
Operating system character set identified as UTF-8.

Copyright (C) 1995, 2018, Oracle and/or its affiliates. All rights reserved.



GGSCI (gg4dbd-source01) 1>
```
2. Create a dump process using the below commands.

```
GGSCI (gg4dbd-source01) 3> edit param P_ORA
```
Add the below content into the pump file and replace the <IPAddress> with the public IP address of your remote target machine.
Here the rmtrail is the remote trail file location where you want to save the trail files from the source Database.(We generally save the trail files in the dirdat directory present inside the Golden Gate installation and the file name can only contain 2 letters).
```
extract P_Ora
--rmthost <IPAddress>, mgrport 7100
rmthost <IPAddress>, mgrport 7100
rmttrail ./dirdat/oraTrails/or
passthru
reportcount every 60 seconds, rate
table pdb1.hr.regions;

```
3. Now add the dump file using below commands.

```
add extract P_Ora, exttrailsource ./dirdat/et
add rmttrail ./dirdat/oraTrails/or, extract P_Ora
```

## Replicat from trail files on the target machine to Kafka topic.

1. We already have a trail file created in the GGBD home. We will be using the same trail file to replicate to OCI Object Storage.

![](images/500/image100_1.pn)

Please refer to Lab 400 for more information.

2. Create the kafka.props file in dirprm folder in your Golden Gate installation folder.
```
[oracle@gg4bd-target01 ggbd_home1]$ cd dirprm
[oracle@gg4bd-target01 dirprm]$ vi kafka.props
```
Copy paste the below text in kafka.props.

```
## RKAFKA properties for Kafka Topic apply
##
gg.handlerlist=kafkahandler
gg.handler.kafkahandler.type=kafka
gg.handler.kafkahandler.KafkaProducerConfigFile=producer.properties
gg.handler.kafkahandler.topicMappingTemplate=oracle
gg.handler.kafkahandler.keyMappingTemplate=${currentTimestamp}
gg.handler.kafkahandler.format=delimitedtext
gg.handler.kafkahandler.format.fieldDelimiter=|
gg.handler.kafkahandler.SchemaTopicName=testSchema
gg.handler.kafkahandler.BlockingSend=true
gg.handler.kafkahandler.includeTokens=false
gg.handler.kafkahandler.mode=tx
goldengate.userexit.timestamp=utc
goldengate.userexit.writers=javawriter
javawriter.stats.display=TRUE
javawriter.stats.full=TRUE
##
gg.log=log4j
gg.log.level=INFO
##
gg.report.time=30sec
##
gg.classpath=dirprm/:/u01/app/kafka/libs/*:/u01/app/kafka/config
##
javawriter.bootoptions=-Xmx512m -Xms32m -Djava.class.path=ggjava/ggjava.jar
gg.handler.kafkahandler.format.pkUpdateHandling=update
```
Save the text using wq!

3. Edit the replicat with the below commands.
```
GGSCI (gg4bd-target01) 1> edit param RKAFKA
```
Add the below content into RKAFKA.
```
REPLICAT RKAFKA
TARGETDB LIBFILE libggjava.so SET property=dirprm/kafka.props
GROUPTRANSOPS 10000
MAP pdb1.hr.regions, TARGET oracle.*;
```

4. Add the replicat with the below command.

```
add replicat RKAFKA, exttrail ./dirdat/oraTrails/or
```
## Test if the changes made in Oracle Database are getting reflected in the Kafka Topic.

1. Go to the Kafka Installation folder.

```
[oracle@gg4bd-target01 app]$ cd kafka
[oracle@gg4bd-target01 kafka]$ ls
bin  config  libs  LICENSE  logs  NOTICE  site-docs
```
2. Start the zookeeper server using the below command.

```
[oracle@gg4bd-target01 kafka]$ bin/zookeeper-server-start.sh config/zookeeper.properties
```
3. Open another terminal and go to the same kafka folder and start the kafka server using below command.

```
[oracle@gg4bd-target01 kafka]$ bin/kafka-server-start.sh config/server.properties
```
4. Open another terminal again and go to the same kafka folder and start the consumer.
```
[oracle@gg4bd-target01 kafka]$ bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic oracle --from-beginning
```
Replace the topic 'oracle' which the topic name that you created.

5. Go and make the below change in Oracle Database on the Source Machine.
![](images/500/image100_1.pn)
