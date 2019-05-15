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
1. Go to GGHOME/AdapterExamples/big-data/hdfs/
2. You should have 2 files hdfs.props and rhdfs.prm (???)







