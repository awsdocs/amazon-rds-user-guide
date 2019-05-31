# Using Oracle GoldenGate with Amazon RDS<a name="Appendix.OracleGoldenGate"></a>

Oracle GoldenGate \(GoldenGate\) is used to collect, replicate, and manage transactional data between databases\. It is a log\-based change data capture \(CDC\) and replication software package used with Oracle databases for online transaction processing \(OLTP\) systems\. GoldenGate creates trail files that contain the most recent changed data from the source database and then pushes these files to the target database\. You can use GoldenGate with Amazon RDS for Active\-Active database replication, zero\-downtime migration and upgrades, disaster recovery, data protection, and in\-region and cross\-region replication\. 

The following are important points to know when working with GoldenGate on Amazon RDS: 
+ You are responsible for setting up and managing GoldenGate for use with Amazon RDS\. 
+ You are responsible for managing GoldenGate licensing \(bring\-your\-own\-license\) for use with Amazon RDS in all AWS regions\. For more information, see [Oracle Licensing](CHAP_Oracle.md#Oracle.Concepts.Licensing)\. 
+ Amazon RDS supports GoldenGate for Oracle Database Standard Edition Two \(SE2\), Standard Edition One \(SE1\), Standard Edition \(SE\), and Enterprise Edition \(EE\)\. 
+ Amazon RDS supports GoldenGate for database version 11\.2\.0\.4, 12\.1\.0\.2, or 12\.2\.0\.1\. 
+ Amazon RDS supports GoldenGate version 11\.2\.1 and later, including 12\.1, 12\.2, and 12\.3\. 
+ Amazon RDS supports migration and replication across Oracle databases using GoldenGate\. We do not support nor prevent customers from migrating or replicating across heterogeneous databases\. 
+ You can use GoldenGate on Amazon RDS Oracle DB instances that use Oracle Transparent Data Encryption \(TDE\)\. To maintain the integrity of replicated data, you should configure encryption on the GoldenGate hub using EBS encrypted volumes or trail file encryption\. You should also configure encryption for data sent between the GoldenGate hub and the source and target database instances\. Amazon RDS Oracle DB instances support encryption with [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md) or [Oracle Native Network Encryption](Appendix.Oracle.Options.NetworkEncryption.md)\. 
+ GoldenGate DDL is supported with GoldenGate version 12\.1 and later when using Integrated capture mode\. 

## Overview<a name="Appendix.OracleGoldenGate.Overview"></a>

The GoldenGate architecture for use with Amazon RDS consists of three decoupled modules\. The source database can be either an on\-premises Oracle database, an Oracle database on an EC2 instance, or an Oracle database on an Amazon RDS DB instance\. Next, the GoldenGate hub, which moves transaction information from the source database to the target database, can be either an EC2 instance with Oracle Database 11\.2\.0\.4 and with GoldenGate 11\.2\.1 installed, or an on\-premises Oracle installation\. You can have more than one EC2 hub, and we recommend that you use two hubs if you are using GoldenGate for cross\-region replication\. Finally, the target database can be either on an Amazon RDS DB instance, on an EC2 instance, or on an on\-premises location\.

GoldenGate on Amazon RDS supports the following common scenarios: 

Scenario 1: An on\-premises Oracle source database and on\-premises GoldenGate hub, that provides data to a target Amazon RDS DB instance\. 

![\[GoldenGate configuration 0 using Amazon RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-gg0.png)

Scenario 2: An on\-premises Oracle database that acts as the source database, connected to an Amazon EC2 instance hub that provides data to a target Amazon RDS DB instance\. 

![\[GoldenGate configuration 1 using Amazon RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-gg1.png)

Scenario 3: An Oracle database on an Amazon RDS DB instance that acts as the source database, connected to an Amazon EC2 instance hub that provides data to a target Amazon RDS DB instance\. 

![\[GoldenGate configuration 2 using Amazon RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-gg2.png)

Scenario 4: An Oracle database on an Amazon EC2 instance that acts as the source database, connected to an Amazon EC2 instance hub that provides data to a target Amazon RDS DB instance\. 

![\[GoldenGate configuration 3 using Amazon RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-gg3.png)

Scenario 5: An Oracle database on an Amazon RDS DB instance connected to an Amazon EC2 instance hub in the same region, connected to an Amazon EC2 instance hub in a different region that provides data to the target Amazon RDS DB instance in the same region as the second EC2 instance hub\. 

![\[GoldenGate configuration 4 using Amazon RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-gg4.png)

**Note**  
Any issues that impact running GoldenGate on an on\-premises environment will also impact running GoldenGate on AWS\. We strongly recommend that you monitor the GoldenGate hub to ensure that `EXTRACT` and `REPLICAT` are resumed if a failover occurs\. Since the GoldenGate hub is run on an Amazon EC2 instance, Amazon RDS does not manage the GoldenGate hub and cannot ensure that it is running\.

You can use GoldenGate using Amazon RDS to upgrade to major versions of Oracle\. For example, you can use GoldenGate using Amazon RDS to upgrade from an Oracle version 8 on\-premises database to an Oracle database running version 11\.2\.0\.4 on an Amazon RDS DB instance\.

To set up GoldenGate using Amazon RDS, you configure the hub on the EC2 instance, and then configure the source and target databases\. The following steps show how to set up GoldenGate for use with Amazon RDS\. Each step is explained in detail in the following sections: 
+ [Setting Up a GoldenGate Hub on EC2](#Appendix.OracleGoldenGate.Hub)
+ [Setting Up a Source Database for Use with GoldenGate on Amazon RDS](#Appendix.OracleGoldenGate.Source)
+ [Setting Up a Target Database for Use with GoldenGate on Amazon RDS](#Appendix.OracleGoldenGate.Target)
+ [Working with the EXTRACT and REPLICAT Utilities of GoldenGate](#Appendix.OracleGoldenGate.ExtractReplicat)

## Setting Up a GoldenGate Hub on EC2<a name="Appendix.OracleGoldenGate.Hub"></a>

There are several steps to creating a GoldenGate hub on an Amazon EC2 instance\. First, you create an EC2 instance with a full installation of Oracle DBMS 11g version 11\.2\.0\.4\. The EC2 instance must also have GoldenGate 11\.2\.1 software installed, and you must have Oracle patch 13328193 installed\. For more information about installing GoldenGate, see the [Oracle documentation](http://docs.oracle.com/cd/E35209_01/index.htm)\.

Since the EC2 instance that is serving as the GoldenGate hub stores and processes the transaction information from the source database into trail files, you must have enough allocated storage to store the trail files\. You must also ensure that the EC2 instance has enough processing power to manage the amount of data being processed and enough memory to store the transaction information before it is written to the trail file\.

The following tasks set up a GoldenGate hub on an Amazon EC2 instance; each task is explained in detail in this section\. The tasks include:
+ Add an alias to the tnsname\.ora file
+ Create the GoldenGate subdirectories
+ Update the GLOBALS parameter file
+ Configure the mgr\.prm file and start the *manager*

Add the following entry to the tnsname\.ora file to create an alias\. For more information on the tnsname\.ora file, see the [Oracle documentation](http://docs.oracle.com/cd/B28359_01/network.111/b28317/tnsnames.htm#NETRF007)\.

```
$ cat /example/config/tnsnames.ora 
TEST=
(DESCRIPTION= 
 (ENABLE=BROKEN)
 (ADDRESS_LIST= 
   (ADDRESS=(PROTOCOL=TCP)(HOST=goldengate-test.abcdef12345.us-west-2.rds.amazonaws.com)(PORT=8200))
 )
 (CONNECT_DATA=
   (SID=ORCL)
 )
)
```

Next, create subdirectories in the GoldenGate directory using the EC2 command line shell and *ggsci*, the GoldenGate command interpreter\. The subdirectories are created under the gg directory and include directories for parameter, report, and checkpoint files\.

```
prompt$ cd /gg
prompt$ ./ggsci
  GGSCI> CREATE SUBDIRS
```

Create a GLOBALS parameter file using the EC2 command line shell\. Parameters that affect all GoldenGate processes are defined in the GLOBALS parameter file\. The following example creates the necessary file:

```
$ cd $GGHOME
$ vi GLOBALS
CheckpointTable oggadm1.oggchkpt
```

The last step in setting up and configuring the GoldenGate hub is to configure the *manager*\. Add the following lines to the *mgr\.prm* file, then start the *manager* using *ggsci*:

```
PORT 8199
PurgeOldExtracts ./dirdat/*, UseCheckpoints, MINKEEPDAYS 5
```

```
GGSCI>  start mgr 
```

Once you have completed these steps, the GoldenGate hub is ready for use\. Next, you set up the source and target databases\.

## Setting Up a Source Database for Use with GoldenGate on Amazon RDS<a name="Appendix.OracleGoldenGate.Source"></a>

 When your source database is running version 11\.2\.0\.4 or later, there are three tasks you need to accomplish to set up a source database for use with GoldenGate: 
+ Set the `compatible` parameter to 11\.2\.0\.4 or later\. 
+ Set the `ENABLE_GOLDENGATE_REPLICATION` parameter to *True*\. This parameter turns on supplemental logging for the source database\. If your source database is on an Amazon RDS DB instance, you must have a parameter group assigned to the DB instance with the `ENABLE_GOLDENGATE_REPLICATION` parameter set to *true*\. For more information about the `ENABLE_GOLDENGATE_REPLICATION` parameter, see the [Oracle documentation](http://docs.oracle.com/cd/E11882_01/server.112/e40402/initparams086.htm#REFRN10346)\.
+ Set the retention period for archived redo logs for the GoldenGate source database\. 
+ Create a GoldenGate user account on the source database\.
+ Grant the necessary privileges to the GoldenGate user\.

The source database must have the `compatible` parameter set to 11\.2\.0\.4 or later\. If you are using an Oracle database on an Amazon RDS DB instance as the source database, you must have a parameter group with the `compatible` parameter set to 11\.2\.0\.4 or later associated with the DB instance\. If you change the `compatible` parameter in a parameter group associated with the DB instance, the change requires an instance reboot\. You can use the following Amazon RDS CLI commands to create a new parameter group and set the `compatible` parameter\. Note that you must associate the new parameter group with the source DB instance:

For Linux, OS X, or Unix:

```
aws rds create-db-parameter-group \
    --db-parameter-group-name example-goldengate \
    --description "Parameters to allow GoldenGate" \
    --db-parameter-group-family oracle-ee-11.2

aws rds modify-db-parameter-group \
    --db-parameter-group-name example-goldengate \
    --parameters "ParameterName=compatible, ParameterValue=11.2.0.4, ApplyMethod=pending-reboot"

aws rds modify-db-instance \
    --db-instance-identifier example-test \
    --db-parameter-group-name example-goldengate \
    --apply-immediately

aws rds reboot-db-instance \
    --db-instance-identifier example-test
```

For Windows:

```
aws rds create-db-parameter-group ^
    --db-parameter-group-name example-goldengate ^
    --description "Parameters to allow GoldenGate" ^
    --db-parameter-group-family oracle-ee-11.2

aws rds modify-db-parameter-group ^
    --db-parameter-group-name example-goldengate ^
    --parameters "ParameterName=compatible, ParameterValue=11.2.0.4, ApplyMethod=pending-reboot"

aws rds modify-db-instance ^
    --db-instance-identifier example-test ^
    --db-parameter-group-name example-goldengate ^
    --apply-immediately

aws rds reboot-db-instance ^
    --db-instance-identifier example-test
```

Always retain the parameter group with the `compatible` parameter\. If you restore an instance from a DB snapshot, you must modify the restored instance to use the parameter group that has a matching or greater `compatible` parameter value\. This should be done as soon as possible after the restore action and will require a reboot of the instance\.

The `ENABLE_GOLDENGATE_REPLICATION` parameter, when set to *True*, turns on supplemental logging for the source database and configures the required GoldenGate permissions\. If your source database is on an Amazon RDS DB instance, you must have a parameter group assigned to the DB instance with the `ENABLE_GOLDENGATE_REPLICATION` parameter set to *true*\. For more information about the `ENABLE_GOLDENGATE_REPLICATION` parameter, see the [Oracle documentation](http://docs.oracle.com/cd/E11882_01/server.112/e40402/initparams086.htm#REFRN10346)\.

The source database must also retain archived redo logs\. For example, the following command sets the retention period for archived redo logs to 24 hours:

```
exec rdsadmin.rdsadmin_util.set_configuration('archivelog retention hours',24); 
```

 The duration for log retention is specified in hours\. The duration should exceed any potential downtime of the source instance or any potential communication/networking issues to the source instance, so that GoldenGate can recover logs from the source instance as needed\. The absolute minimum value required is one \(1\) hour of logs retained\.

  A log retention setting that is too small will result in the following message:

```
ERROR OGG-02028  Failed to attach to logmining server OGG$<extract_name> error 26927 - ORA-26927: altering an outbound server with a remote capture is not allowed. 
```

Because these logs are retained on your DB instance, you need to ensure that you have enough storage available on your instance to accommodate the log files\. To see how much space you have used in the last "X" hours, use the following query, replacing "X" with the number of hours\.

```
select sum(blocks * block_size) bytes from v$archived_log 
   where next_time>=sysdate-X/24 and dest_id=1;
```

GoldenGate runs as a database user and must have the appropriate database privileges to access the redo and archive logs for the source database, so you must create a GoldenGate user account on the source database\. For more information about the permissions for a GoldenGate user account, see the sections 4, section 4\.4, and table 4\.1 in the [Oracle documentation](http://docs.oracle.com/cd/E35209_01/doc.1121/e35957.pdf)\.

The following statements create a user account named *oggadm1*: 

```
CREATE tablespace administrator;
CREATE USER oggadm1  IDENTIFIED BY "XXXXXX"  
   default tablespace ADMINISTRATOR temporary tablespace TEMP;
```

Finally, grant the necessary privileges to the GoldenGate user account\. The following statements grant privileges to a user named *oggadm1*:

```
grant create session, alter session to oggadm1;
grant resource to oggadm1;
grant select any dictionary to oggadm1;
grant flashback any table to oggadm1;
grant select any table to oggadm1;
grant select_catalog_role to <RDS instance master username> with admin option;
exec RDSADMIN.RDSADMIN_UTIL.GRANT_SYS_OBJECT ('DBA_CLUSTERS', 'OGGADM1');
grant execute on dbms_flashback to oggadm1;
grant select on SYS.v_$database to oggadm1;
grant alter any table to oggadm1;

EXEC DBMS_GOLDENGATE_AUTH.GRANT_ADMIN_PRIVILEGE (grantee=>'OGGADM1',
   privilege_type=>'capture',
   grant_select_privileges=>true, 
   do_grants=>TRUE);
```

## Setting Up a Target Database for Use with GoldenGate on Amazon RDS<a name="Appendix.OracleGoldenGate.Target"></a>

The following tasks set up a target DB instance for use with GoldenGate:
+ Set the `compatible` parameter to 11\.2\.0\.4 or later
+  Set the ENABLE\_GOLDENGATE\_REPLICATION parameter to *True*\. If your target database is on an Amazon RDS DB instance, you must have a parameter group assigned to the DB instance with the ENABLE\_GOLDENGATE\_REPLICATION parameter set to *true*\. For more information about the ENABLE\_GOLDENGATE\_REPLICATION parameter, see the [Oracle documentation](http://docs.oracle.com/cd/E11882_01/server.112/e40402/initparams086.htm#REFRN10346) \. 
+ Create and manage a GoldenGate user account on the target database
+ Grant the necessary privileges to the GoldenGate user

 GoldenGate runs as a database user and must have the appropriate database privileges, so you must create a GoldenGate user account on the target database\. The following statements create a user named *oggadm1*: 

```
create tablespace administrator;
create tablespace administrator_idx;
CREATE USER oggadm1  IDENTIFIED BY "XXXXXX" 
   default tablespace ADMINISTRATOR 
   temporary tablespace TEMP;
alter user oggadm1 quota unlimited on ADMINISTRATOR;
alter user oggadm1 quota unlimited on ADMINISTRATOR_IDX;
```

Finally, grant the necessary privileges to the GoldenGate user account\. The following statements grant privileges to a user named *oggadm1*:

```
grant create session        to oggadm1;
grant alter session         to oggadm1;
grant CREATE CLUSTER        to oggadm1;
grant CREATE INDEXTYPE      to oggadm1;
grant CREATE OPERATOR       to oggadm1;
grant CREATE PROCEDURE      to oggadm1;
grant CREATE SEQUENCE       to oggadm1;
grant CREATE TABLE          to oggadm1;
grant CREATE TRIGGER        to oggadm1;
grant CREATE TYPE           to oggadm1;
grant select any dictionary to oggadm1;
grant create any table      to oggadm1;
grant alter any table       to oggadm1;
grant lock any table        to oggadm1;
grant select any table      to oggadm1;
grant insert any table      to oggadm1;
grant update any table      to oggadm1;
grant delete any table      to oggadm1;

EXEC DBMS_GOLDENGATE_AUTH.GRANT_ADMIN_PRIVILEGE 
   (grantee=>'OGGADM1',privilege_type=>'apply',
   grant_select_privileges=>true, do_grants=>TRUE);
```

## Working with the EXTRACT and REPLICAT Utilities of GoldenGate<a name="Appendix.OracleGoldenGate.ExtractReplicat"></a>

The GoldenGate utilities `EXTRACT` and `REPLICAT` work together to keep the source and target databases in sync via incremental transaction replication using trail files\. All changes that occur on the source database are automatically detected by `EXTRACT`, then formatted and transferred to trail files on the GoldenGate on\-premises or EC2\-instance hub\. After initial load is completed, the data is read from these files and replicated to the target database by the `REPLICAT` utility\.

### Running GoldenGate's `EXTRACT` Utility<a name="Appendix.OracleGoldenGate.Extract"></a>

The `EXTRACT` utility retrieves, converts, and outputs data from the source database to trail files\. `EXTRACT` queues transaction details to memory or to temporary disk storage\. When the transaction is committed to the source database, `EXTRACT` flushes all of the transaction details to a trail file for routing to the GoldenGate on\-premises or EC2\-instance hub and then to the target database\.

The following tasks enable and start the `EXTRACT` utility:
+ Configure the `EXTRACT` parameter file on the GoldenGate hub \(on\-premises or EC2 instance\)\. The following listing shows an example `EXTRACT` parameter file\.

  ```
  EXTRACT EABC
  SETENV (ORACLE_SID=ORCL)
  SETENV (NLSLANG=AL32UTF8)
   
  USERID oggadm1@TEST, PASSWORD XXXXXX
  EXTTRAIL /path/to/goldengate/dirdat/ab 
   
  IGNOREREPLICATES
  GETAPPLOPS
  TRANLOGOPTIONS EXCLUDEUSER OGGADM1
  	 
  TABLE EXAMPLE.TABLE;
  ```
+ On the GoldenGate hub, launch the GoldenGate command line interface \(*ggsci*\)\. Log into the source database\. The following example shows the format for logging in:

  ```
   dblogin userid <user>@<db tnsname> 
  ```
+ Add a checkpoint table for the database:

  ```
  add checkpointtable 
  ```
+ Add transdata to turn on supplemental logging for the database table:

  ```
  add trandata <user>.<table> 
  ```

  Alternatively, you can add transdata to turn on supplemental logging for all tables in the database:

  ```
  add trandata <user>.* 
  ```
+ Using the `ggsci` command line, enable the `EXTRACT` utility using the following commands:

  ```
  add extract <extract name> tranlog, INTEGRATED tranlog, begin now
  add exttrail <path-to-trail-from-the param-file> 
     extract <extractname-from-paramfile>, 
     MEGABYTES Xm
  ```
+ Register the `EXTRACT` utility with the database so that the archive logs are not deleted\. This allows you to recover old, uncommitted transactions if necessary\. To register the `EXTRACT` utility with the database, use the following command:

  ```
   register EXTRACT <extract process name>, DATABASE  
  ```
+ To start the `EXTRACT` utility, use the following command:

  ```
  start <extract process name> 
  ```

### Running GoldenGate's `REPLICAT` Utility<a name="Appendix.OracleGoldenGate.Replicat"></a>

The `REPLICAT` utility is used to "push" transaction information in the trail files to the target database\.

The following tasks enable and start the `REPLICAT` utility:
+ Configure the `REPLICAT` parameter file on the GoldenGate hub \(on\-premises or EC2 instance\)\. The following listing shows an example `REPLICAT` parameter file\.

  ```
  REPLICAT RABC
  SETENV (ORACLE_SID=ORCL)
  SETENV (NLSLANG=AL32UTF8)
   
  USERID oggadm1@TARGET, password XXXXXX
   
  ASSUMETARGETDEFS
  MAP EXAMPLE.TABLE, TARGET EXAMPLE.TABLE;
  ```
+ Launch the GoldenGate command line interface \(ggsci\)\. Log into the target database\. The following example shows the format for logging in:

  ```
   dblogin userid <user>@<db tnsname>  
  ```
+ Using the *ggsci* command line, add a checkpoint table\. Note that the user indicated should be the GoldenGate user account, not the target table schema owner\. The following example creates a checkpoint table named *gg\_checkpoint*\.

  ```
  add checkpointtable <user>.gg_checkpoint  
  ```
+ To enable the `REPLICAT` utility, use the following command:

  ```
  add replicat <replicat name> EXTTRAIL <extract trail file> CHECKPOINTTABLE <user>.gg_checkpoint  
  ```
+ To start the `REPLICAT` utility, use the following command:

  ```
  start <replicat name> 
  ```

## Troubleshooting Issues When Using GoldenGate with Amazon RDS<a name="Appendix.OracleGoldenGate.Troubleshooting"></a>

This section explains the most common issues when using GoldenGate with Amazon RDS\.

**Topics**
+ [Log Retention](#Appendix.OracleGoldenGate.Troubleshooting.Logs)
+ [GoldenGate appears to be properly configured but replication is not working](#w4aac30c95c11c19c10)

### Log Retention<a name="Appendix.OracleGoldenGate.Troubleshooting.Logs"></a>

You must have log retention enabled\. If you do not, or if the retention value is too small, you will see the following message:

```
2014-03-06 06:17:27  ERROR   OGG-00446  error 2 (No such file or directory) 
opening redo log /rdsdbdata/db/GGTEST3_A/onlinelog/o1_mf_2_9k4bp1n6_.log 
for sequence 1306Not able to establish initial position for begin time 2014-03-06 06:16:55.
```

### GoldenGate appears to be properly configured but replication is not working<a name="w4aac30c95c11c19c10"></a>

For pre\-existing tables, GoldenGate needs to be told which SCN it should work from\. Take the following steps to fix this issue:
+ Launch the GoldenGate command line interface \(ggsci\)\. Log into the source database\. The following example shows the format for logging in:

  ```
  dblogin userid <user>@<db tnsname> 
  ```
+ Using the ggsci command line, set up the start SCN for the `EXTRACT` process\. The following example sets the SCN to 223274 for the extract:

  ```
  ALTER EXTRACT <extract process name> SCN 223274
  start <extract process name>
  ```
+ Log into the target database\. The following example shows the format for logging in:

  ```
  dblogin userid <user>@<db tnsname> 
  ```
+ Using the ggsci command line, set up the start SCN for the `REPLICAT` process\. The following example sets the SCN to 223274 for the `REPLICAT`:

  ```
  start <replicat process name> atcsn 223274 
  ```