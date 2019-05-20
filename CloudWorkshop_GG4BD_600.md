# Lab 600 -  MySQL to HDFS (json text format)
![](images/100/image100_0.png)


## Before You Begin

### Introduction
In this lab, we will see how to replicate data from MySQL to HDFS using GoldenGate for Big Data.

### Objectives
- GoldenGate replication from MySQL to HDFS

### Time to Complete
Approximately 30 minutes

### What Do You Need?
Your will need:
- Oracle Cloud URL
- Oracle Cloud User and Password
- Installed GoldenGate for MySQL on the source instance where MySQL is running
- Installed GoldenGate for Big Data on the target instance where HDFS is installed

## MySQL Source Configuration
### STEP 1: Log in to your Oracle Cloud console and find the public IP address of the source instance. Open a terminal or a Putty and ssh into that source instance with the following command:
```
$ ssh -i <path_to_private_key> opc@<public_IP_address>
```

### STEP 2: Check where GoldenGate `mgr` process is running.
1. Change to `oracle` user
    ```
    $ sudo su - oracle
    ```
2. Search for the process `mgr`
    ```
    $ ps -ef | grep mgr
    ```
3. You see that `mgr` is running and GoldenGate for MySQL is installed in the location `/u01/app/oracle/product/18.1.0_GGMySQL`.

    ![](images/600/Lab600-image1.jpg)

4. Change directory to the path we found in the previous step.

    ```
    $ cd /u01/app/oracle/product/18.1.0_GGMySQL
    ```
5. Start GGSCI
    ```
    $ ./ggsci
    ```
6. Steps to set up Extract on MySQL - **TODO**


## HDFS Target Configuration
1. Go to `/u01/app/ggbd_home1/AdapterExamples/big-data/hdfs`. You should have 2 files `hdfs.props` and `rhdfs.prm`.
    ```
    $ cd /u01/app/ggbd_home1/AdapterExamples/big-data/hdfs
    $ ls -al
    ```

2. Take back up of these files.
    ```
    $ cp hdfs.props hdfs.props_bkp
    $ cp rhdfs.prm rhdfs.prm_bkp
    ```

3. Edit `hdfs.props` file

    ```
    $ vi hdfs.props
    ```
    * Enter the following contents:
    ```
    javawriter.stats.full=TRUE

    gg.log=log4j
    gg.log.level=INFO

    gg.report.time=30sec

    #Sample gg.classpath for Apache Hadoop
    #gg.classpath=/var/lib/hadoop/share/hadoop/common/*:/var/lib/hadoop/share/hadoop/common/lib/*:/var/lib/hadoop/share/hadoop/hdfs/*:/var/lib/hadoop/share/hadoop/hdfs/lib/*:/var/lib/hadoop/etc/hadoop/:

    gg.classpath=ggjava/ggjava.jar:/u01/app/hadoop-2.7.6/etc/hadoop/:/u01/app/hadoop-2.7.6/share/hadoop/common/*:/u01/app/hadoop-2.7.6/share/hadoop/common/lib/*:/u01/app/hadoop-2.7.6/share/hadoop/hdfs/*:/u01/app/hadoop-2.7.6/share/hadoop/hdfs/lib/*:/u01/app/jars/hadoop-hdfs-2.4.0.jar
    #Sample gg.classpath for CDH
    #gg.classpath=/opt/cloudera/parcels/CDH/lib/hadoop/client/*:/etc/hadoop/conf
    #Sample gg.classpath for HDP
    #gg.classpath=/usr/hdp/current/hadoop-client/client/*:/etc/hadoop/conf

    javawriter.bootoptions=-Xmx512m -Xms32m -Djava.class.path=ggjava/ggjava.jar
    ```

    * Save and quit.
4. Edit rhdfs.prm file.
    ```
    $ vi rhdfs.prm
    ```

    * Enter the following contents:
    ```
    REPLICAT rhdfs
    ---------------------------------------------------------------------------------------
    -- Trail file for this example is located in "AdapterExamples/trail" directory
    -- Command to add REPLICAT
    -- add replicat rhdfs, exttrail ./dirdat/eb
    ---------------------------------------------------------------------------------------
    TARGETDB LIBFILE libggjava.so SET property=dirprm/hdfs.props
    REPORTCOUNT EVERY 1 MINUTES, RATE
    GROUPTRANSOPS 10000
    MAP employees.*, TARGET employees.*;
    ```

    * Save and quit.

5. Move these files to `/u01/app/ggbd_home1/dirprm` directory.
    ```
    $ mv /u01/app/ggbd_home1/AdapterExamples/big-data/hdfs/* /u01/app/ggbd_home1/dirprm
    ```
    









