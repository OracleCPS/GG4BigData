# Lab 500 -  MySQL to File Writer Handler (csv / parquet format)
![](images/500/image100_0.png)


## Before You Begin

### Introduction
In this lab we will use goldengate for bigdata file writer handler to replicate data into OCI cloud in delimitedtext and parquet format.

### Objectives
- Goldengate BigData replication to OCI Object Storage in dsv and parquet format

### Time to Complete
Approximately 60 minutes

### What Do You Need?
Your will need:
- Goldengate for Bigdata
- Oracle Cloud Object Storage Connection Detals and Library files.
- for parquet format , need parquet libraries

### STEP 1: Setting up the Environment For Connection to OCI Cloud.
    
In this step we will set up the compute instace to be able to connect to OCI cloud. We need to Download the OCI libraries and keep it in a directory. 

1. We will create a directory oci and keep the file here.

```
[opc@gg4bd-target01 ~]$ sudo su - oracle
Last login: Mon May  6 09:22:44 GMT 2019 on pts/0
[oracle@gg4bd-target01 ~]$ mkdir  ~/.oci
```

2. Create config file with OCI Cloud connection details like User , fingerprint, region. You can get these details from your instructor.

```
[oracle@gg4bd-target01 Downloads]$ cd
[oracle@gg4bd-target01 ~]$ clear
[oracle@gg4bd-target01 ~]$ cd ~/.oci
[oracle@gg4bd-target01 .oci]$ vi config
```

And add the below details in the file
```
[DEFAULT]
user=ocid1.user.oc1..aaaaaaaaqpcwz5cgjseweox6cyiikgxlgeypxuqlu7xlvxndp2frejmpzmaa
fingerprint=73:71:47:43:ba:87:1a:7d:ac:f8:a9:c1:62:8b:1a:58
region=us-ashburn-1
key_file=/home/oracle/.oci/oci_api_key.pem
tenancy=ocid1.tenancy.oc1..aaaaaaaa7l6wfqydzz6otbjnhrloxf4tezittufwjlwbaoxvajvkr6hyxmba
```

3. Now Copy the .pem file from /home/oracle/Downloads to ~/.oci directory. If this is not available in this location then get it from your instructor.

```
[oracle@gg4bd-target01 .oci]$ cp /home/oracle/Downloads/oci_api_key.pem ~/.oci
[oracle@gg4bd-target01 .oci]$ ls -lrt
total 8
-rw-rw-r--. 1 oracle oracle  304 May  6 09:39 config
-rw-rw-r--. 1 oracle oracle 1680 May  6 09:42 oci_api_key.pem
[oracle@gg4bd-target01 .oci]$
```

4. We need to unzip and keep the OCI java sdk file in a location which are needed for replicat to connect. You can download it from

```  
    https://docs.cloud.oracle.com/iaas/Content/API/SDKDocs/javasdk.htm
```
In our environment we have it downloaded in /home/oracle/Downloads. So Will create a directory for jars and copy the file into that directory.

```
[oracle@gg4bd-target01 ggbd_home1]$ mkdir -p /u01/app/jars/oci_libs
[oracle@gg4bd-target01 ggbd_home1]$ cp /home/oracle/Downloads/oci-java-sdk.zip /u01/app/jars/oci_libs/
```

Now unzip the file in the location.

```
[oracle@gg4bd-target01 ggbd_home1]$ cd /u01/app/jars/oci_libs/
[oracle@gg4bd-target01 ggbd_home1]$ unzip oci-java-sdk.zip
[oracle@gg4bd-target01 oci_libs]$ ls -lrt
total 113044
-rw-r--r--. 1 oracle oracle     13144 Apr 15 20:56 LICENSE.txt
drwxr-xr-x. 2 oracle oracle      4096 Apr 15 20:56 examples
-rw-r--r--. 1 oracle oracle     26068 Apr 15 20:56 CHANGELOG.md
drwxr-xr-x. 3 oracle oracle      4096 Apr 15 21:17 apidocs
drwxr-xr-x. 3 oracle oracle      4096 Apr 15 21:18 third-party
drwxr-xr-x. 2 oracle oracle      4096 Apr 15 21:18 lib
drwxr-xr-x. 4 oracle oracle      4096 Apr 15 21:19 shaded
drwxr-xr-x. 4 oracle oracle      4096 Apr 15 21:20 addons
-rwxr-xr-x. 1 oracle oracle 115685847 May  6 10:12 oci-java-sdk.zip
```

### STEP 2: Goldengate Replicat Setup for delimitedtext format in OCI Obejct Storage.

1. We already have a trail file created in the GGBD home. We will be using the same trail file to replicate to OCI Object Storage.

![](images/500/image100_1.png)


2. Add the replicat with the below commands.

```
GGSCI (gg4bd-target01) 4> add replicat RFWDSV, exttrail ./dirdat/eb
REPLICAT added.

GGSCI (gg4bd-target01) 5> edit param RFWDSV
```

Add the below parameters in the parameter file :
```
REPLICAT RFWDSV
-----------------------------------------------------------------------------------------
-- Trail file for this example is located in "AdapterExamples/trail" directory
-- Command to add REPLICAT
-- add replicat RFWDSV, exttrail ./dirdat/eb
-- SETENV(GGS_JAVAUSEREXIT_CONF = 'dirprm/fw.props')
-----------------------------------------------------------------------------------------
TARGETDB LIBFILE libggjava.so SET property=dirprm/rfwdsv.props
REPORTCOUNT EVERY 1 MINUTES, RATE
GROUPTRANSOPS 1000
MAP employees.*, TARGET employees.*;
```

3. Now edit the dirprm/rfwdsv.props file with the below parameters. You can use sample property files found in $GGBD_HOME/AdapterExamples/big-data/filewriter .

```
GGSCI (gg4bd-target01) 8> exit
[oracle@gg4bd-target01 ggbd_home1]$ cd dirprm
[oracle@gg4bd-target01 dirprm]$ vi rfwdsv.props
```

Below are the parametrs we will be using.

```
gg.handlerlist=filewriter

#The File Writer Handler
gg.handler.filewriter.type=filewriter
gg.handler.filewriter.mode=op
gg.handler.filewriter.pathMappingTemplate=./dirout
gg.handler.filewriter.stateFileDirectory=./dirsta
gg.handler.filewriter.fileNameMappingTemplate=${fullyQualifiedTableName}_${currentTimestamp}.txt
gg.handler.filewriter.fileRollInterval=7m
gg.handler.filewriter.finalizeAction=delete
gg.handler.filewriter.inactivityRollInterval=7m
gg.handler.filewriter.format=delimitedtext
gg.handler.filewriter.format.pkUpdateHandling=update
gg.handler.filewriter.partitionByTable=true
gg.handler.filewriter.includetokens=true
gg.handler.filewriter.partitionByTable=true
#Selecting the Parquet Event Handler
gg.handler.filewriter.eventHandler=oci
gg.handler.filewriter.rollOnShutdown=true

#The OCI Event handler
gg.eventhandler.oci.type=oci
gg.eventhandler.oci.configFilePath=~/.oci/config
gg.eventhandler.oci.profile=DEFAULT
gg.eventhandler.oci.namespace=orasenatdpltintegration02
gg.eventhandler.oci.region=us-ashburn-1
gg.eventhandler.oci.compartmentID=ocid1.compartment.oc1..aaaaaaaanunsv7u5me5gn3mcq6xlyknaebaj7uxaf42rzpo37xhfrjgqokaq
gg.eventhandler.oci.pathMappingTemplate=${fullyQualifiedTableName}
gg.eventhandler.oci.bucketMappingTemplate=GG4BD
gg.eventhandler.oci.fileNameMappingTemplate=${fullyQualifiedTableName}_${currentTimestamp}.csv
gg.eventhandler.oci.finalizeAction=NONE

goldengate.userexit.timestamp=utc
goldengate.userexit.writers=javawriter
javawriter.stats.display=TRUE
javawriter.stats.full=TRUE

gg.log=log4j
gg.log.level=INFO
gg.includeggtokens=true
gg.report.time=30sec

#Set the classpath here
#User TODO - Need the AWS Java SDK, Parquet Dependencies, HDFS Client Dependencies
gg.classpath=/u01/app/jars/oci_libs/lib/*:/u01/app/jars/oci_libs/third-party/lib/*
javawriter.bootoptions=-Xmx512m -Xms32m -Djava.class.path=.:ggjava/ggjava.jar:./dirprm
```

4. Now goto ggsci prompt and you will see the replicat RFWDSV. start the replicat and see the data in the OCI object storage.

