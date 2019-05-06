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

### STEP 1: Setting up the environment for connection to OCI cloud.
    
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





