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
    
2. Add the replicat with the below commands by logging into ggsci prmopt

```
GGSCI (gg4bd-target01) 4> add replicat rjdbc, exttrail ./dirdat/eb
REPLICAT added.

GGSCI (gg4bd-target01) 5> edit param rjdbc
```

Add the below parameters in the parameter file :

```
REPLICAT rjdbc
----------------------------------------------------------------------------------------
-- Trail file for this example is located in "AdapterExamples/trail" directory
-- Command to add REPLICAT
-- add replicat rjdbc, exttrail ./dirdat/eb
--TARGETDB LIBFILE libggjava.so SET property=dirprm/jdbc_mysql_with_mdp.props
----------------------------------------------------------------------------------------
TARGETDB LIBFILE libggjava.so SET property=dirprm/jdbc_oracle_with_mdp.props
REPORTCOUNT EVERY 1 MINUTES, RATE
GROUPTRANSOPS 1000
MAP employees.*, TARGET PDB1.EMPLOYEES..*;
```

3. Now edit the dirprm/rfwcsv.props file with the below parameters. You can use sample property files found in $GGBD_HOME/AdapterExamples/big-data/filewriter.