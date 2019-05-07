# Lab 1100 -  MySQL to Oracle (using jdbc)
![](images/1100/image100_0.png)

## Before You Begin

### Introduction
In this lab we will use goldengate for bigdata Java Database Connectivity (JDBC) Handler to replicate source transactional data to a target or database.

### Objectives
- Provision Provision Golden Gate for Big Data 

### Time to Complete
Approximately 30 minutes

### What Do You Need?
Your will need:
- Goldengate for Bigdata
- Oracle JDBC Java Driver

### STEP 1: Setting up the Environment For Connection to OCI Cloud.

1. In this step we will download the oracle jdbc jar and create a directory and unzip the files in that directory.

        We already have the jdbc drive downloaded ojdbc8-full.tar.gz in the location /home/oracle/Downloads 
```
[opc@gg4bd-target01 ~]$ sudo su - oracle
Last login: Tue May  7 14:53:22 GMT 2019 on pts/3
[oracle@gg4bd-target01 ~]$ mkdir -p /u01/app/jars/oracle_jdbc

[oracle@gg4bd-target01 ~]$ cd /u01/app/jars/oracle_jdbc
[oracle@gg4bd-target01 oracle_jdbc]$ cp /home/oracle/Downloads/ojdbc8-full.tar.gz /u01/app/jars/oracle_jdbc/

[oracle@gg4bd-target01 oracle_jdbc]$ ls -lrt
total 7532
-rwxr-xr-x. 1 oracle oracle 7709868 May  7 14:57 ojdbc8-full.tar.gz

[oracle@gg4bd-target01 oracle_jdbc]$ tar -xvzf ojdbc8-full.tar.gz
```
    
2. 