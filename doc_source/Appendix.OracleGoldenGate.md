# Using Oracle GoldenGate with Amazon RDS<a name="Appendix.OracleGoldenGate"></a>

Oracle GoldenGate \(GoldenGate\) collects, replicates, and manages transactional data between databases\. It is a log\-based change data capture \(CDC\) and replication software package used with Oracle databases for online transaction processing \(OLTP\) systems\. GoldenGate creates trail files that contain the most recent changed data from the source database and then pushes these files to the target database\.

You can use GoldenGate with Amazon RDS to do the following:
+ Active\-Active database replication
+ Disaster recovery
+ Data protection
+ In\-Region and cross\-Region replication
+ Zero\-downtime migration and upgrades
**Note**  
You can use GoldenGate using Amazon RDS to upgrade to major versions of Oracle Database\. For example, you can use GoldenGate with Amazon RDS to upgrade from an Oracle Database 11g on\-premises database to Oracle Database 19c on an Amazon RDS DB instance\.

**Topics**
+ [Supported versions and licensing options for Oracle GoldenGate](#Appendix.OracleGoldenGate.licensing)
+ [Requirements and limitations for Oracle GoldenGate](#Appendix.OracleGoldenGate.requirements)
+ [Oracle GoldenGate architecture](#Appendix.OracleGoldenGate.Overview)
+ [Setting up Oracle GoldenGate](#Appendix.OracleGoldenGate.setting-up)
+ [Working with the EXTRACT and REPLICAT utilities of GoldenGate](#Appendix.OracleGoldenGate.ExtractReplicat)
+ [Troubleshooting Oracle GoldenGate](#Appendix.OracleGoldenGate.Troubleshooting)

## Supported versions and licensing options for Oracle GoldenGate<a name="Appendix.OracleGoldenGate.licensing"></a>

Amazon RDS supports the following editions and versions with Oracle GoldenGate:
+ Oracle Database Standard Edition Two \(SE2\)
+ Oracle Database Enterprise Edition \(EE\)
+ Oracle GoldenGate version 11\.2\.1 and later
+ Oracle GoldenGate DDL is supported with Oracle GoldenGate version 12\.1 and later when using Integrated capture mode

You are responsible for managing GoldenGate licensing \(BYOL\) for use with Amazon RDS in all AWS Regions\. For more information, see [Oracle licensing options](Oracle.Concepts.Licensing.md)\.

## Requirements and limitations for Oracle GoldenGate<a name="Appendix.OracleGoldenGate.requirements"></a>

When working with GoldenGate on Amazon RDS, consider the following requirements and limitations: 
+ You are responsible for setting up and managing GoldenGate for use with Amazon RDS\. 
+ Amazon RDS supports migration and replication across Oracle databases using GoldenGate\. We do not support nor prevent customers from migrating or replicating across heterogeneous databases\.
+ You can use GoldenGate on Amazon RDS for Oracle DB instances that use Oracle Transparent Data Encryption \(TDE\)\. To maintain the integrity of replicated data, configure encryption on the Oracle GoldenGate hub using Amazon EBS encrypted volumes or trail file encryption\. Also configure encryption for data sent between the Oracle GoldenGate hub and the source and target database instances\. Amazon RDS for Oracle DB instances support encryption with [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md) or [Oracle native network encryption](Appendix.Oracle.Options.NetworkEncryption.md)\.

## Oracle GoldenGate architecture<a name="Appendix.OracleGoldenGate.Overview"></a>

The Oracle GoldenGate architecture for use with Amazon RDS consists of the following decoupled modules:

Source database  
Your source database can be either an on\-premises Oracle database, an Oracle database on an Amazon EC2 instance, or an Oracle database on an Amazon RDS DB instance\.

Oracle GoldenGate hub  
An Oracle GoldenGate hub moves transaction information from the source database to the target database\. Your hub can be either of the following:  
+ An Amazon EC2 instance with Oracle Database and GoldenGate installed
+ An on\-premises Oracle installation
You can have more than one Amazon EC2 hub\. We recommend that you use two hubs if you use GoldenGate for cross\-Region replication\.

Target database  
Your target database can be on either an Amazon RDS DB instance, an Amazon EC2 instance, or an on\-premises location\.

The following sections describe common scenarios for GoldenGate on Amazon RDS\.

**Topics**
+ [On\-premises source database and Oracle GoldenGate hub](#Appendix.OracleGoldenGate.on-prem-source-gg-hub)
+ [On\-premises source database and Amazon EC2 hub](#Appendix.OracleGoldenGate.on-prem-source-ec2-hub)
+ [Amazon RDS source database and Amazon EC2 hub](#Appendix.OracleGoldenGate.rds-source-ec2-hub)
+ [Amazon EC2 source database and Amazon EC2 hub](#Appendix.OracleGoldenGate.ec2-source-ec2-hub)
+ [Amazon EC2 hubs in different AWS Regions](#Appendix.OracleGoldenGate.cross-region-hubs)

### On\-premises source database and Oracle GoldenGate hub<a name="Appendix.OracleGoldenGate.on-prem-source-gg-hub"></a>

In this scenario, an on\-premises Oracle source database and on\-premises Oracle GoldenGate hub provides data to a target Amazon RDS DB instance\. 

![\[GoldenGate configuration 0 using Amazon RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-gg0.png)

### On\-premises source database and Amazon EC2 hub<a name="Appendix.OracleGoldenGate.on-prem-source-ec2-hub"></a>

In this scenario, an on\-premises Oracle database acts as the source database\. It's connected to an Amazon EC2 instance hub\. This hub provides data to a target RDS for Oracle DB instance\.

![\[GoldenGate configuration 1 using Amazon RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-gg1.png)

### Amazon RDS source database and Amazon EC2 hub<a name="Appendix.OracleGoldenGate.rds-source-ec2-hub"></a>

In this scenario, an RDS for Oracle DB instance acts as the source database\. It's connected to an Amazon EC2 instance hub\. This hub provides data to a target RDS for Oracle DB instance\. 

![\[GoldenGate configuration 2 using Amazon RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-gg2.png)

### Amazon EC2 source database and Amazon EC2 hub<a name="Appendix.OracleGoldenGate.ec2-source-ec2-hub"></a>

In this scenario, an Oracle database on an Amazon EC2 instance acts as the source database\. It's connected to an Amazon EC2 instance hub\. This hub provides data to a target RDS for Oracle DB instance\. 

![\[GoldenGate configuration 3 using Amazon RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-gg3.png)

### Amazon EC2 hubs in different AWS Regions<a name="Appendix.OracleGoldenGate.cross-region-hubs"></a>

In this scenario, an Oracle database on an Amazon RDS DB instance is connected to an Amazon EC2 instance hub in the same AWS Region\. The hub is connected to an Amazon EC2 instance hub in a different AWS Region\. This second hub provides data to the target RDS for Oracle DB instance in the same AWS Region as the second Amazon EC2 instance hub\.

![\[GoldenGate configuration 4 using Amazon RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-gg4.png)

**Note**  
Any issues that affect running GoldenGate on an on\-premises environment also affect running GoldenGate on AWS\. We strongly recommend that you monitor the Oracle GoldenGate hub to ensure that `EXTRACT` and `REPLICAT` are resumed if a failover occurs\. Because the Oracle GoldenGate hub is run on an Amazon EC2 instance, Amazon RDS does not manage the Oracle GoldenGate hub and cannot ensure that it is running\.

## Setting up Oracle GoldenGate<a name="Appendix.OracleGoldenGate.setting-up"></a>

To set up GoldenGate using Amazon RDS, you configure the hub on the Amazon EC2 instance, and then configure the source and target databases\. The following sections show how to set up GoldenGate for use with Amazon RDS\.

**Topics**
+ [Setting up a GoldenGate hub on Amazon EC2](#Appendix.OracleGoldenGate.Hub)
+ [Setting up a source database for use with GoldenGate on Amazon RDS](#Appendix.OracleGoldenGate.Source)
+ [Setting up a target database for use with GoldenGate on Amazon RDS](#Appendix.OracleGoldenGate.Target)

### Setting up a GoldenGate hub on Amazon EC2<a name="Appendix.OracleGoldenGate.Hub"></a>

To create a GoldenGate hub on an Amazon EC2 instance, you complete several steps\. First, you create an Amazon EC2 instance with a full client installation of Oracle RDBMS\. The Amazon EC2 instance must also have Oracle GoldenGate software installed\. The exact software versions depend on the source and target database versions\. For more information about installing GoldenGate, see the [Oracle documentation](http://docs.oracle.com/cd/E35209_01/index.htm)

The Amazon EC2 instance that serves as the Oracle GoldenGate hub stores and processes the transaction information from the source database into trail files\. To support this process, make sure that you meet the following conditions:
+ You have allocated enough storage for the trail files
+ The Amazon EC2 instance has enough processing power to manage the amount of data\.
+ The EC2 instance has enough memory to store the transaction information before it's written to the trail file\.

The following tasks set up a GoldenGate hub on an Amazon EC2 instance; each task is explained in detail in this section:

1. Create the Oracle GoldenGate subdirectories\.

1. Update the GLOBALS parameter file\.

1. Configure the mgr\.prm file and start the manager\.

Create subdirectories in the Oracle GoldenGate directory using the Amazon EC2 command line shell and *ggsci*, the Oracle GoldenGate command interpreter\. The subdirectories are created under the gg directory and include directories for parameter, report, and checkpoint files\.

```
prompt$ cd /gg
prompt$ ./ggsci
  GGSCI> CREATE SUBDIRS
```

Create a GLOBALS parameter file using the Amazon EC2 command line shell\. Parameters that affect all GoldenGate processes are defined in the GLOBALS parameter file\. The following example creates the necessary file:

```
$ cd $GGHOME
$ vi GLOBALS
CheckpointTable oggadm1.oggchkpt
```

The last step in setting up and configuring the Oracle GoldenGate hub is to configure the *manager*\. Add the following lines to the *mgr\.prm* file, then start the *manager* using *ggsci*:

```
PORT 8199
PurgeOldExtracts ./dirdat/*, UseCheckpoints, MINKEEPDAYS 5
```

```
GGSCI>  start mgr 
```

Once you have completed these steps, the Oracle GoldenGate hub is ready for use\. Next, you set up the source and target databases\.

### Setting up a source database for use with GoldenGate on Amazon RDS<a name="Appendix.OracleGoldenGate.Source"></a>

 When your source database is running Oracle Database 12c or later, complete the following tasks to set up a source database for use with GoldenGate: 

1. Set the `ENABLE_GOLDENGATE_REPLICATION` parameter to *True*\. This parameter turns on supplemental logging for the source database\. If your source database is on an Amazon RDS DB instance, make sure that you have a parameter group assigned to the DB instance with the `ENABLE_GOLDENGATE_REPLICATION` parameter set to *true*\. For more information about the `ENABLE_GOLDENGATE_REPLICATION` parameter, see the [Oracle documentation](http://docs.oracle.com/cd/E11882_01/server.112/e40402/initparams086.htm#REFRN10346)\.

1. Set the retention period for archived redo logs for the Oracle GoldenGate source database\. 

1. Create a GoldenGate user account on the source database\.

1. Grant the necessary privileges to the Oracle GoldenGate user\.

1. Add a TNS alias for the source database to the `tnsnames.ora` file on the Oracle GoldenGate hub\.

#### Enable supplemental logging on the source DB<a name="Appendix.OracleGoldenGate.Source.Logging"></a>

The `ENABLE_GOLDENGATE_REPLICATION` parameter, when set to *True*, turns on supplemental logging for the source database and configures the required GoldenGate permissions\. If your source database is on an Amazon RDS DB instance, make sure that you have a parameter group assigned to the DB instance with `ENABLE_GOLDENGATE_REPLICATION` set to *true*\. For more information about `ENABLE_GOLDENGATE_REPLICATION`, see the [Oracle documentation](http://docs.oracle.com/cd/E11882_01/server.112/e40402/initparams086.htm#REFRN10346)\.

#### Set the log retention period on the source DB<a name="Appendix.OracleGoldenGate.Source.Retention"></a>

The source database must also retain archived redo logs\. For example, set the retention period for archived redo logs to 24 hours\.

```
exec rdsadmin.rdsadmin_util.set_configuration('archivelog retention hours',24); 
```

Specify the duration for log retention in hours\. The duration should exceed any potential downtime of the source instance, any potential period of communication, and any potential period of networking issues for the source instance\. Such a duration lets Oracle GoldenGate recover logs from the source instance as needed\.

The absolute minimum value required is one hour of logs retained\. If you don't have log retention enabled, or if the retention value is too small, you receive the following message\.

```
2014-03-06 06:17:27  ERROR   OGG-00446  error 2 (No such file or directory) 
opening redo log /rdsdbdata/db/GGTEST3_A/onlinelog/o1_mf_2_9k4bp1n6_.log 
for sequence 1306Not able to establish initial position for begin time 2014-03-06 06:16:55.
```

The logs are retained on your DB instance\. Ensure that you have sufficient storage on your instance for the files\. To see how much space you have used in the last *X* hours, use the following query, replacing *X* with the number of hours\.

```
SELECT SUM(BLOCKS * BLOCK_SIZE) BYTES FROM V$ARCHIVED_LOG 
   WHERE NEXT_TIME>=SYSDATE-X/24 AND DEST_ID=1;
```

#### Create a user account on the source<a name="Appendix.OracleGoldenGate.Source.Account"></a>

GoldenGate runs as a database user and requires the appropriate database privileges to access the redo and archive logs for the source database\. To provide these, create a GoldenGate user account on the source database\. For more information about the permissions for a GoldenGate user account, see the sections 4, section 4\.4, and table 4\.1 in the [Oracle documentation](http://docs.oracle.com/cd/E35209_01/doc.1121/e35957.pdf)\.

The following statements create a user account named *oggadm1*\. 

```
CREATE TABLESPACE administrator;
CREATE USER oggadm1  IDENTIFIED BY "password"  
   DEFAULT TABLESPACE ADMINISTRATOR TEMPORARY TABLESPACE TEMP;
```

#### Grant account privileges on the source DB<a name="Appendix.OracleGoldenGate.Source.Privileges"></a>

Grant the necessary privileges to the Oracle GoldenGate user account using the SQL command `grant` and the `rdsadmin.rdsadmin_util` procedure `grant_sys_object`\. The following statements grant privileges to a user named *oggadm1*\.

```
GRANT CREATE SESSION, ALTER SESSION TO oggadm1;
GRANT RESOURCE TO oggadm1;
GRANT SELECT ANY DICTIONARY TO oggadm1;
GRANT FLASHBACK ANY TABLE TO oggadm1;
GRANT SELECT ANY TABLE TO oggadm1;
GRANT SELECT_CATALOG_ROLE TO rds_master_user_name WITH ADMIN OPTION;
exec rdsadmin.rdsadmin_util.grant_sys_object ('DBA_CLUSTERS', 'OGGADM1');
GRANT EXECUTE ON DBMS_FLASHBACK TO oggadm1;
GRANT SELECT ON SYS.V_$DATABASE TO oggadm1;
GRANT ALTER ANY TABLE TO oggadm1;
```

Finally, grant the privileges needed by a user account to be a GoldenGate administrator\. The package that you use to perform the grant, `dbms_goldengate_auth` or `rdsadmin_dbms_goldengate_auth`, depends on the Oracle DB engine version\.
+ For Oracle DB versions that are *earlier than* Oracle Database 12c Release 2 \(12\.2\), run the following PL/SQL program\.

  ```
  exec dbms_goldengate_auth.grant_admin_privilege (grantee=>'OGGADM1',
     privilege_type=>'capture',
     grant_select_privileges=>true, 
     do_grants=>TRUE);
  ```
+ For Oracle DB versions that are *later than or equal to* Oracle Database 12c Release 2 \(12\.2\), which requires patch level 12\.2\.0\.1\.ru\-2019\-04\.rur\-2019\-04\.r1 or later, run the following PL/SQL program\.

  ```
  exec rdsadmin.rdsadmin_dbms_goldengate_auth.grant_admin_privilege (grantee=>'OGGADM1',
     privilege_type=>'capture',
     grant_select_privileges=>true, 
     do_grants=>TRUE);
  ```

  To revoke privileges, use the procedure `revoke_admin_privilege` in the same package\.

#### Add a TNS alias for the source DB<a name="Appendix.OracleGoldenGate.Source.TNS"></a>

Add the following entry to `$ORACLE_HOME/network/admin/tnsnames.ora` in the Oracle Home to be used by the `EXTRACT` process\. For more information on the `tnsnames.ora` file, see the [Oracle documentation](https://docs.oracle.com/en/database/oracle/oracle-database/19/netrf/local-naming-parameters-in-tns-ora-file.html#GUID-7F967CE5-5498-427C-9390-4A5C6767ADAA)\.

```
OGGSOURCE=
   (DESCRIPTION= 
        (ENABLE=BROKEN)
        (ADDRESS_LIST= 
            (ADDRESS=(PROTOCOL=TCP)(HOST=goldengate-source.abcdef12345.us-west-2.rds.amazonaws.com)(PORT=8200)))
        (CONNECT_DATA=(SID=ORCL))
    )
```

### Setting up a target database for use with GoldenGate on Amazon RDS<a name="Appendix.OracleGoldenGate.Target"></a>

The following tasks set up a target DB instance for use with GoldenGate:

1.  Set the `ENABLE_GOLDENGATE_REPLICATION` parameter to `TRUE`\. If your target database is on an Amazon RDS DB instance, make sure that you have a parameter group assigned to the DB instance with the `ENABLE_GOLDENGATE_REPLICATION` parameter set to `TRUE`\. For more information about the `ENABLE_GOLDENGATE_REPLICATION` parameter, see the [Oracle documentation](http://docs.oracle.com/cd/E11882_01/server.112/e40402/initparams086.htm#REFRN10346)\. 

1. Create and manage a GoldenGate user account on the target database

1. Grant the necessary privileges to the Oracle GoldenGate user

1. Add a TNS alias for the target database to tnsnames\.ora on the Oracle GoldenGate hub\.

#### Create a user account on the target DB<a name="Appendix.OracleGoldenGate.Target.User"></a>

 GoldenGate runs as a database user and requires the appropriate database privileges\. To make sure it has these, create a GoldenGate user account on the target database\.

The following statements create a user named *oggadm1*: 

```
CREATE TABLESPACE administrator;
CREATE TABLESPACE administrator_idx;
CREATE USER oggadm1  IDENTIFIED BY "password" 
   DEFAULT TABLESPACE administrator 
   TEMPORARY TABLESPACE temp;
ALTER USER oggadm1 QUOTA UNLIMITED ON administrator;
ALTER USER oggadm1 QUOTA UNLIMITED ON administrator_idx;
```

#### Grant account privileges on the target DB<a name="Appendix.OracleGoldenGate.Target.Privileges"></a>

Grant necessary privileges to the Oracle GoldenGate user account on the target DB\. In the following example, you grant privileges to *oggadm1*\.

```
GRANT CREATE SESSION        TO oggadm1;
GRANT ALTER SESSION         TO oggadm1;
GRANT CREATE CLUSTER        TO oggadm1;
GRANT CREATE INDEXTYPE      TO oggadm1;
GRANT CREATE OPERATOR       TO oggadm1;
GRANT CREATE PROCEDURE      TO oggadm1;
GRANT CREATE SEQUENCE       TO oggadm1;
GRANT CREATE TABLE          TO oggadm1;
GRANT CREATE TRIGGER        TO oggadm1;
GRANT CREATE TYPE           TO oggadm1;
GRANT SELECT ANY DICTIONARY TO oggadm1;
GRANT CREATE ANY TABLE      TO oggadm1;
GRANT ALTER ANY TABLE       TO oggadm1;
GRANT LOCK ANY TABLE        TO oggadm1;
GRANT SELECT ANY TABLE      TO oggadm1;
GRANT INSERT ANY TABLE      TO oggadm1;
GRANT UPDATE ANY TABLE      TO oggadm1;
GRANT DELETE ANY TABLE      TO oggadm1;
```

Finally, grant the privileges needed by a user account to be a GoldenGate administrator\. The package that you use to perform the grant, `dbms_goldengate_auth` or `rdsadmin_dbms_goldengate_auth`, depends on the Oracle DB engine version\.
+ For Oracle DB versions that are *earlier than* Oracle Database 12c Release 2 \(12\.2\), run the following PL/SQL program\.

  ```
  exec dbms_goldengate_auth.grant_admin_privilege (grantee=>'OGGADM1',
     privilege_type=>'capture',
     grant_select_privileges=>true, 
     do_grants=>TRUE);
  ```
+ For Oracle DB versions that are *later than or equal to* Oracle Database 12c Release 2 \(12\.2\), which requires patch level 12\.2\.0\.1\.ru\-2019\-04\.rur\-2019\-04\.r1 or later, run the following PL/SQL program\.

  ```
  exec rdsadmin.rdsadmin_dbms_goldengate_auth.grant_admin_privilege (grantee=>'OGGADM1',
     privilege_type=>'capture',
     grant_select_privileges=>true, 
     do_grants=>TRUE);
  ```

  To revoke privileges, use the procedure `revoke_admin_privilege` in the same package\.

#### Add a TNS alias for the target DB<a name="Appendix.OracleGoldenGate.Target.TNS"></a>

Add the following entry to `$ORACLE_HOME/network/admin/tnsnames.ora` in the Oracle Home to be used by the `REPLICAT` process\. For more information on the `tnsname.ora` file, see the [Oracle documentation](https://docs.oracle.com/en/database/oracle/oracle-database/19/netrf/local-naming-parameters-in-tns-ora-file.html#GUID-7F967CE5-5498-427C-9390-4A5C6767ADAA)\.

```
OGGTARGET=
    (DESCRIPTION= 
        (ENABLE=BROKEN)
        (ADDRESS_LIST= 
            (ADDRESS=(PROTOCOL=TCP)(HOST=goldengate-target.abcdef12345.us-west-2.rds.amazonaws.com)(PORT=8200)))
        (CONNECT_DATA=(SID=ORCL))
    )
```

## Working with the EXTRACT and REPLICAT utilities of GoldenGate<a name="Appendix.OracleGoldenGate.ExtractReplicat"></a>

The GoldenGate utilities `EXTRACT` and `REPLICAT` work together to keep the source and target databases in sync via incremental transaction replication using trail files\. All changes that occur on the source database are automatically detected by `EXTRACT`, then formatted and transferred to trail files on the Oracle GoldenGate on\-premises or EC2\-instance hub\. After initial load is completed, the data is read from these files and replicated to the target database by the `REPLICAT` utility\.

### Running the Oracle GoldenGate EXTRACT utility<a name="Appendix.OracleGoldenGate.Extract"></a>

The `EXTRACT` utility retrieves, converts, and outputs data from the source database to trail files\. `EXTRACT` queues transaction details to memory or to temporary disk storage\. When the transaction is committed to the source database, `EXTRACT` flushes all of the transaction details to a trail file\. The trail file routes these details to the Oracle GoldenGate on\-premises or the EC2 instance hub and then to the target database\.

The following tasks enable and start the `EXTRACT` utility:

1. Configure the `EXTRACT` parameter file on the Oracle GoldenGate hub \(on\-premises or EC2 instance\)\. The following listing shows an example `EXTRACT` parameter file\.

   ```
   EXTRACT EABC
   SETENV (ORACLE_SID=ORCL)
   SETENV (NLSLANG=AL32UTF8)
    
   USERID oggadm1@OGGSOURCE, PASSWORD XXXXXX
   EXTTRAIL /path/to/goldengate/dirdat/ab 
    
   IGNOREREPLICATES
   GETAPPLOPS
   TRANLOGOPTIONS EXCLUDEUSER OGGADM1
   	 
   TABLE EXAMPLE.TABLE;
   ```

1. On the Oracle GoldenGate hub, launch the Oracle GoldenGate command line interface \(*ggsci*\)\. Log into the source database\. The following example shows the format for logging in:

   ```
   dblogin userid <user>@<db tnsname> 
   ```

1. Add a checkpoint table for the database:

   ```
   add checkpointtable 
   ```

1. Add transdata to turn on supplemental logging for the database table:

   ```
   add trandata <user>.<table> 
   ```

   Alternatively, you can add transdata to turn on supplemental logging for all tables in the database:

   ```
   add trandata <user>.* 
   ```

1. Using the `ggsci` command line, enable the `EXTRACT` utility using the following commands:

   ```
   add extract <extract name> tranlog, INTEGRATED tranlog, begin now
   add exttrail <path-to-trail-from-the param-file> 
      extract <extractname-from-paramfile>, 
      MEGABYTES Xm
   ```

1. Register the `EXTRACT` utility with the database so that the archive logs are not deleted\. This allows you to recover old, uncommitted transactions if necessary\. To register the `EXTRACT` utility with the database, use the following command:

   ```
    register EXTRACT <extract process name>, DATABASE  
   ```

1. To start the `EXTRACT` utility, use the following command:

   ```
   start <extract process name> 
   ```

### Running the Oracle GoldenGate REPLICAT utility<a name="Appendix.OracleGoldenGate.Replicat"></a>

The `REPLICAT` utility is used to "push" transaction information in the trail files to the target database\.

The following tasks enable and start the `REPLICAT` utility:

1. Configure the `REPLICAT` parameter file on the Oracle GoldenGate hub \(on\-premises or EC2 instance\)\. The following listing shows an example `REPLICAT` parameter file\.

   ```
   REPLICAT RABC
   SETENV (ORACLE_SID=ORCL)
   SETENV (NLSLANG=AL32UTF8)
    
   USERID oggadm1@OGGTARGET, password XXXXXX
    
   ASSUMETARGETDEFS
   MAP EXAMPLE.TABLE, TARGET EXAMPLE.TABLE;
   ```

1. Launch the Oracle GoldenGate command line interface \(ggsci\)\. Log into the target database\. The following example shows the format for logging in\.

   ```
   dblogin userid <user>@<db tnsname>  
   ```

1. Using the *ggsci* command line, add a checkpoint table\. The user indicated should be the Oracle GoldenGate user account, not the target table schema owner\. The following example creates a checkpoint table named *gg\_checkpoint*\.

   ```
   add checkpointtable <user>.gg_checkpoint  
   ```

1. To enable the `REPLICAT` utility, use the following command\.

   ```
   add replicat <replicat name> EXTTRAIL <extract trail file> CHECKPOINTTABLE <user>.gg_checkpoint  
   ```

1. To start the `REPLICAT` utility, use the following command\.

   ```
   start <replicat name> 
   ```

## Troubleshooting Oracle GoldenGate<a name="Appendix.OracleGoldenGate.Troubleshooting"></a>

This section explains the most common issues when using Oracle GoldenGate with Amazon RDS\.

**Topics**
+ [Log retention](#Appendix.OracleGoldenGate.Troubleshooting.Logs)
+ [Oracle GoldenGate appears to be properly configured but replication is not working](#Appendix.OracleGoldenGate.Troubleshooting.Replication)
+ [Integrated REPLICAT slow due to query on sys\."\_DBA\_APPLY\_CDR\_INFO"](#Appendix.OracleGoldenGate.IR)

### Log retention<a name="Appendix.OracleGoldenGate.Troubleshooting.Logs"></a>

To work with Oracle GoldenGate and Amazon RDS, make sure that you have log retention enabled\. 

Specify the duration for log retention in hours\. The duration should exceed any potential downtime of the source instance, any potential period of communication, and any potential period of networking issues for the source instance\. Such a duration lets Oracle GoldenGate recover logs from the source instance as needed\.

The absolute minimum value required is one hour of logs retained\. If you don't have log retention enabled, or if the retention value is too small, you receive the following message\.

```
2014-03-06 06:17:27  ERROR   OGG-00446  error 2 (No such file or directory) 
opening redo log /rdsdbdata/db/GGTEST3_A/onlinelog/o1_mf_2_9k4bp1n6_.log 
for sequence 1306Not able to establish initial position for begin time 2014-03-06 06:16:55.
```

### Oracle GoldenGate appears to be properly configured but replication is not working<a name="Appendix.OracleGoldenGate.Troubleshooting.Replication"></a>

For pre\-existing tables, Oracle GoldenGate must be told which SCN it should work from\. Take the following steps to fix this issue:

1. Launch the Oracle GoldenGate command line interface \(ggsci\)\. Log into the source database\. The following example shows the format for logging\.

   ```
   dblogin userid <user>@<db tnsname> 
   ```

1. Using the ggsci command line, set up the start SCN for the `EXTRACT` process\. The following example sets the SCN to 223274 for the extract\.

   ```
   ALTER EXTRACT <extract process name> SCN 223274
   start <extract process name>
   ```

1. Log in to the target database\. The following example shows the format for logging in\.

   ```
   dblogin userid <user>@<db tnsname> 
   ```

1. Using the ggsci command line, set up the start SCN for the `REPLICAT` process\. The following example sets the SCN to 223274 for the `REPLICAT`\.

   ```
   start <replicat process name> atcsn 223274 
   ```

### Integrated REPLICAT slow due to query on sys\."\_DBA\_APPLY\_CDR\_INFO"<a name="Appendix.OracleGoldenGate.IR"></a>

Oracle GoldenGate Conflict Detection and Resolution \(CDR\) provides basic conflict resolution routines\. For example, CDR can resolve a unique conflict for an `INSERT` statement\.

When CDR resolves a collision, it can insert records into the exception table `_DBA_APPLY_CDR_INFO` temporarily\. Integrated `REPLICAT` deletes these records later\. In a rare scenario, the integrated `REPLICAT` can process a large number of collisions, but a new integrated `REPLICAT` does not replace it\. Instead of being removed, the existing rows in `_DBA_APPLY_CDR_INFO` are orphaned\. Any new integrated `REPLICAT` processes slow down because they are querying orphaned rows in `_DBA_APPLY_CDR_INFO`\.

To remove all rows from `_DBA_APPLY_CDR_INFO`, use the Amazon RDS procedure `rdsadmin_util.truncate_apply$_cdr_info`\. This procedure is released as part of the October 2020 release and patch update\. The procedure is available in the following database versions:
+ [Version 19\.0\.0\.0\.ru\-2020\-10\.rur\-2020\-10\.r1](Appendix.Oracle.RU-RUR.19.0.0.0.md#Appendix.Oracle.RU-RUR.19.0.0.0.ru-2020-10.rur-2020-10.r1)
+ [Version 18\.0\.0\.0\.ru\-2020\-10\.rur\-2020\-10\.r1](Appendix.Oracle.RU-RUR.18.0.0.0.md#Appendix.Oracle.RU-RUR.18.0.0.0.ru-2020-10.rur-2020-10.r1)
+ [Version 12\.2\.0\.1\.ru\-2020\-10\.rur\-2020\-10\.r1](Appendix.Oracle.RU-RUR.12.2.0.1.md#Appendix.Oracle.RU-RUR.12.2.0.1.ru-2020-10.rur-2020-10.r1)
+ [Version 12\.1\.0\.2\.v22](Appendix.Oracle.PatchComposition.12.1.0.2.md#Appendix.Oracle.PatchComposition.12.1.0.2.v22)

The following example truncates the table `_DBA_APPLY_CDR_INFO`\.

```
SET SERVEROUTPUT ON SIZE 2000
EXEC rdsadmin.rdsadmin_util.truncate_apply$_cdr_info;
```