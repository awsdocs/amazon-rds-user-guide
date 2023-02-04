# Using Oracle GoldenGate with Amazon RDS for Oracle<a name="Appendix.OracleGoldenGate"></a>

Oracle GoldenGate collects, replicates, and manages transactional data between databases\. It is a log\-based change data capture \(CDC\) and replication software package used with databases for online transaction processing \(OLTP\) systems\. Oracle GoldenGate creates trail files that contain the most recent changed data from the source database\. It then pushes these files to the server, where a process converts the trail file into standard SQL to be applied to the target database\.

Oracle GoldenGate with RDS for Oracle supports the following features:
+ Active\-Active database replication
+ Disaster recovery
+ Data protection
+ In\-Region and cross\-Region replication
+ Zero\-downtime migration and upgrades
+ Data replication between an RDS for Oracle DB instance and a non\-Oracle database
**Note**  
For a list of supported databases, see [Oracle Fusion Middleware Supported System Configurations](https://www.oracle.com/middleware/technologies/fusion-certification.html) in the Oracle documentation\.

You can use Oracle GoldenGate with RDS for Oracle to upgrade to major versions of Oracle Database\. For example, you can use Oracle GoldenGate to upgrade from an Oracle Database 11g on\-premises database to Oracle Database 19c on an Amazon RDS DB instance\.

**Topics**
+ [Supported versions and licensing options for Oracle GoldenGate](#Appendix.OracleGoldenGate.licensing)
+ [Requirements and limitations for Oracle GoldenGate](#Appendix.OracleGoldenGate.requirements)
+ [Oracle GoldenGate architecture](#Appendix.OracleGoldenGate.Overview)
+ [Setting up Oracle GoldenGate](#Appendix.OracleGoldenGate.setting-up)
+ [Working with the EXTRACT and REPLICAT utilities of Oracle GoldenGate](#Appendix.OracleGoldenGate.ExtractReplicat)
+ [Monitoring Oracle GoldenGate](#Appendix.OracleGoldenGate.Monitoring)
+ [Troubleshooting Oracle GoldenGate](#Appendix.OracleGoldenGate.Troubleshooting)

## Supported versions and licensing options for Oracle GoldenGate<a name="Appendix.OracleGoldenGate.licensing"></a>

You can use Standard Edition Two \(SE2\) or Enterprise Edition \(EE\) of RDS for Oracle with Oracle GoldenGate version 12c and higher\. You can use the following Oracle GoldenGate features:
+ Oracle GoldenGate Remote Capture \(extract\) is supported\.
+ Capture \(extract\) is supported on RDS for Oracle DB instances that use the traditional non\-CDB database architecture\. Oracle GoldenGate Remote PDB capture is supported on Oracle Database 21c container databases \(CDBs\)\.
+ Oracle GoldenGate Remote Delivery \(replicat\) is supported on RDS for Oracle DB instances that use either the non\-CDB or CDB architectures\. Remote Delivery supports Integrated Replicat, Parallel Replicat, Coordinated Replicat, and classic Replicat\.
+ RDS for Oracle supports the Classic and Microservices architectures of Oracle GoldenGate\.
+ Oracle GoldenGate DDL and Sequence value replication is supported when using Integrated capture mode\.

You are responsible for managing Oracle GoldenGate licensing \(BYOL\) for use with Amazon RDS in all AWS Regions\. For more information, see [RDS for Oracle licensing options](Oracle.Concepts.Licensing.md)\.

## Requirements and limitations for Oracle GoldenGate<a name="Appendix.OracleGoldenGate.requirements"></a>

When you're working with Oracle GoldenGate and RDS for Oracle, consider the following requirements and limitations: 
+ You're responsible for setting up and managing Oracle GoldenGate for use with RDS for Oracle\. 
+ You're responsible for setting up an Oracle GoldenGate version that is certified with the source and the target databases\. For more information, see [Oracle Fusion Middleware Supported System Configurations](https://www.oracle.com/middleware/technologies/fusion-certification.html) in the Oracle documentation\.
+ You can use Oracle GoldenGate on many different AWS environments for many different use cases\. If you have a support\-related issue relating to Oracle GoldenGate, contact Oracle Support Services\.
+ You can use Oracle GoldenGate on RDS for Oracle DB instances that use Oracle Transparent Data Encryption \(TDE\)\. To maintain the integrity of replicated data, configure encryption on the Oracle GoldenGate hub using Amazon EBS encrypted volumes or trail file encryption\. Also configure encryption for data sent between the Oracle GoldenGate hub and the source and target database instances\. RDS for Oracle DB instances support encryption with [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md) or [Oracle native network encryption](Appendix.Oracle.Options.NetworkEncryption.md)\.

## Oracle GoldenGate architecture<a name="Appendix.OracleGoldenGate.Overview"></a>

The Oracle GoldenGate architecture for use with Amazon RDS consists of the following decoupled modules:

Source database  
Your source database can be either an on\-premises Oracle database, an Oracle database on an Amazon EC2 instance, or an Oracle database on an Amazon RDS DB instance\.

Oracle GoldenGate hub  
An Oracle GoldenGate hub moves transaction information from the source database to the target database\. Your hub can be either of the following:  
+ An Amazon EC2 instance with Oracle Database and Oracle GoldenGate installed
+ An on\-premises Oracle installation
You can have more than one Amazon EC2 hub\. We recommend that you use two hubs if you use Oracle GoldenGate for cross\-Region replication\.

Target database  
Your target database can be on either an Amazon RDS DB instance, an Amazon EC2 instance, or an on\-premises location\.

The following sections describe common scenarios for Oracle GoldenGate on Amazon RDS\.

**Topics**
+ [On\-premises source database and Oracle GoldenGate hub](#Appendix.OracleGoldenGate.on-prem-source-gg-hub)
+ [On\-premises source database and Amazon EC2 hub](#Appendix.OracleGoldenGate.on-prem-source-ec2-hub)
+ [Amazon RDS source database and Amazon EC2 hub](#Appendix.OracleGoldenGate.rds-source-ec2-hub)
+ [Amazon EC2 source database and Amazon EC2 hub](#Appendix.OracleGoldenGate.ec2-source-ec2-hub)
+ [Amazon EC2 hubs in different AWS Regions](#Appendix.OracleGoldenGate.cross-region-hubs)

### On\-premises source database and Oracle GoldenGate hub<a name="Appendix.OracleGoldenGate.on-prem-source-gg-hub"></a>

In this scenario, an on\-premises Oracle source database and on\-premises Oracle GoldenGate hub provides data to a target Amazon RDS DB instance\. 

![\[Oracle GoldenGate configuration 0 using Amazon RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-gg0.png)

### On\-premises source database and Amazon EC2 hub<a name="Appendix.OracleGoldenGate.on-prem-source-ec2-hub"></a>

In this scenario, an on\-premises Oracle database acts as the source database\. It's connected to an Amazon EC2 instance hub\. This hub provides data to a target RDS for Oracle DB instance\.

![\[Oracle GoldenGate configuration 1 using Amazon RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-gg1.png)

### Amazon RDS source database and Amazon EC2 hub<a name="Appendix.OracleGoldenGate.rds-source-ec2-hub"></a>

In this scenario, an RDS for Oracle DB instance acts as the source database\. It's connected to an Amazon EC2 instance hub\. This hub provides data to a target RDS for Oracle DB instance\.

![\[Oracle GoldenGate configuration 2 using Amazon RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-gg2.png)

### Amazon EC2 source database and Amazon EC2 hub<a name="Appendix.OracleGoldenGate.ec2-source-ec2-hub"></a>

In this scenario, an Oracle database on an Amazon EC2 instance acts as the source database\. It's connected to an Amazon EC2 instance hub\. This hub provides data to a target RDS for Oracle DB instance\.

![\[Oracle GoldenGate configuration 3 using Amazon RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-gg3.png)

### Amazon EC2 hubs in different AWS Regions<a name="Appendix.OracleGoldenGate.cross-region-hubs"></a>

In this scenario, an Oracle database on an Amazon RDS DB instance is connected to an Amazon EC2 instance hub in the same AWS Region\. The hub is connected to an Amazon EC2 instance hub in a different AWS Region\. This second hub provides data to the target RDS for Oracle DB instance in the same AWS Region as the second Amazon EC2 instance hub\.

![\[Oracle GoldenGate configuration 4 using Amazon RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-gg4.png)

**Note**  
Any issues that affect running Oracle GoldenGate on an on\-premises environment also affect running Oracle GoldenGate on AWS\. We strongly recommend that you monitor the Oracle GoldenGate hub to ensure that `EXTRACT` and `REPLICAT` are resumed if a failover occurs\. Because the Oracle GoldenGate hub is run on an Amazon EC2 instance, Amazon RDS does not manage the Oracle GoldenGate hub and cannot ensure that it is running\.

## Setting up Oracle GoldenGate<a name="Appendix.OracleGoldenGate.setting-up"></a>

To set up Oracle GoldenGate using Amazon RDS, configure the hub on an Amazon EC2 instance, and then configure the source and target databases\. The following sections give an example of how to set up Oracle GoldenGate for use with Amazon RDS for Oracle\.

**Topics**
+ [Setting up an Oracle GoldenGate hub on Amazon EC2](#Appendix.OracleGoldenGate.Hub)
+ [Setting up a source database for use with Oracle GoldenGate on Amazon RDS](#Appendix.OracleGoldenGate.Source)
+ [Setting up a target database for use with Oracle GoldenGate on Amazon RDS](#Appendix.OracleGoldenGate.Target)

### Setting up an Oracle GoldenGate hub on Amazon EC2<a name="Appendix.OracleGoldenGate.Hub"></a>

To create an Oracle GoldenGate hub on an Amazon EC2 instance, you first create an Amazon EC2 instance with a full client installation of Oracle RDBMS\. The Amazon EC2 instance must also have Oracle GoldenGate software installed\. The Oracle GoldenGate software versions depend on the source and target database versions\. For more information about installing Oracle GoldenGate, see the [Oracle GoldenGate documentation](https://docs.oracle.com/en/middleware/goldengate/core/index.html)\.

The Amazon EC2 instance that serves as the Oracle GoldenGate hub stores and processes the transaction information from the source database into trail files\. To support this process, make sure that you meet the following requirements:
+ You have allocated enough storage for the trail files\.
+ The Amazon EC2 instance has enough processing power to manage the amount of data\.
+ The EC2 instance has enough memory to store the transaction information before it's written to the trail file\.

**To set up an Oracle GoldenGate classic architecture hub on an Amazon EC2 instance**

1. Create subdirectories in the Oracle GoldenGate directory\.

   In the Amazon EC2 command line shell, start `ggsci`, the Oracle GoldenGate command interpreter\. The `CREATE SUBDIRS` command creates subdirectories under the `/gg` directory for parameter, report, and checkpoint files\.

   ```
   prompt$ cd /gg
   prompt$ ./ggsci
   
   GGSCI> CREATE SUBDIRS
   ```

1. Configure the `mgr.prm` file\.

   The following example adds lines to the `$GGHOME/dirprm/mgr.prm` file\.

   ```
   PORT 8199
   PurgeOldExtracts ./dirdat/*, UseCheckpoints, MINKEEPDAYS 5
   ```

1. Start the manager\.

   The following example starts `ggsci` and runs the `start mgr` command\.

   ```
   GGSCI> start mgr
   ```

The Oracle GoldenGate hub is now ready for use\.

### Setting up a source database for use with Oracle GoldenGate on Amazon RDS<a name="Appendix.OracleGoldenGate.Source"></a>

When your source database is running Oracle Database 12c or later, complete the following tasks to set up a source database for use with Oracle GoldenGate\.

**Topics**
+ [Step 1: Turn on supplemental logging on the source database](#Appendix.OracleGoldenGate.Source.Logging)
+ [Step 2: Set the ENABLE\_GOLDENGATE\_REPLICATION initialization parameter to true](#Appendix.OracleGoldenGate.Source.enable-gg-rep)
+ [Step 3: Set the log retention period on the source database](#Appendix.OracleGoldenGate.Source.Retention)
+ [Step 4: Create an Oracle GoldenGate user account on the source database](#Appendix.OracleGoldenGate.Source.Account)
+ [Step 5: Grant user account privileges on the source database](#Appendix.OracleGoldenGate.Source.Privileges)
+ [Step 6: Add a TNS alias for the source database](#Appendix.OracleGoldenGate.Source.TNS)

#### Step 1: Turn on supplemental logging on the source database<a name="Appendix.OracleGoldenGate.Source.Logging"></a>

To turn on the minimum database\-level supplemental logging, run the following PL/SQL procedure: 

```
EXEC rdsadmin.rdsadmin_util.alter_supplemental_logging(p_action => 'ADD')
```

#### Step 2: Set the ENABLE\_GOLDENGATE\_REPLICATION initialization parameter to true<a name="Appendix.OracleGoldenGate.Source.enable-gg-rep"></a>

When you set the `ENABLE_GOLDENGATE_REPLICATION` initialization parameter to `true`, it allows database services to support logical replication\. If your source database is on an Amazon RDS DB instance, make sure that you have a parameter group assigned to the DB instance with the `ENABLE_GOLDENGATE_REPLICATION` initialization parameter set to `true`\. For more information about the `ENABLE_GOLDENGATE_REPLICATION` initialization parameter, see the [Oracle Database documentation](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/ENABLE_GOLDENGATE_REPLICATION.html)\.

#### Step 3: Set the log retention period on the source database<a name="Appendix.OracleGoldenGate.Source.Retention"></a>

Make sure that you configure the source database to retain archived redo logs\. Consider the following guidelines:
+ Specify the duration for log retention in hours\. The minimum value is one hour\.
+ Set the duration to exceed any potential downtime of the source DB instance, any potential period of communication, and any potential period of networking issues for the source instance\. Such a duration lets Oracle GoldenGate recover logs from the source instance as needed\.
+ Ensure that you have sufficient storage on your instance for the files\.

For example, set the retention period for archived redo logs to 24 hours\.

```
EXEC rdsadmin.rdsadmin_util.set_configuration('archivelog retention hours',24)
```

If you don't have log retention enabled, or if the retention value is too small, you receive an error message similar to the following\.

```
2022-03-06 06:17:27  ERROR   OGG-00446  error 2 (No such file or directory) 
opening redo log /rdsdbdata/db/GGTEST3_A/onlinelog/o1_mf_2_9k4bp1n6_.log for sequence 1306 
Not able to establish initial position for begin time 2022-03-06 06:16:55.
```

Because your DB instance retains your archived redo logs, make sure that you have sufficient space for the files\. To see how much space you have used in the last *num\_hours* hours, run the following query, replacing *num\_hours* with the number of hours\.

```
SELECT SUM(BLOCKS * BLOCK_SIZE) BYTES FROM V$ARCHIVED_LOG 
   WHERE NEXT_TIME>=SYSDATE-num_hours/24 AND DEST_ID=1;
```

#### Step 4: Create an Oracle GoldenGate user account on the source database<a name="Appendix.OracleGoldenGate.Source.Account"></a>

Oracle GoldenGate runs as a database user and requires the appropriate database privileges to access the redo and archived redo logs for the source database\. To provide these, create a user account on the source database\. For more information about the permissions for an Oracle GoldenGate user account, see the [Oracle documentation](https://docs.oracle.com/en/middleware/goldengate/core/19.1/oracle-db/establishing-oracle-goldengate-credentials.html#GUID-79122058-27B0-4FB6-B3DC-B7D1B67EB053)\.

The following statements create a user account named `oggadm1`\. 

```
CREATE TABLESPACE administrator;
CREATE USER oggadm1  IDENTIFIED BY "password"
   DEFAULT TABLESPACE ADMINISTRATOR TEMPORARY TABLESPACE TEMP;
ALTER USER oggadm1 QUOTA UNLIMITED ON administrator;
```

#### Step 5: Grant user account privileges on the source database<a name="Appendix.OracleGoldenGate.Source.Privileges"></a>

In this task, you grant necessary account privileges for database users on your source database\.

**To grant account privileges on the source database**

1. Grant the necessary privileges to the Oracle GoldenGate user account using the SQL command `grant` and the `rdsadmin.rdsadmin_util` procedure `grant_sys_object`\. The following statements grant privileges to a user named `oggadm1`\.

   ```
   GRANT CREATE SESSION, ALTER SESSION TO oggadm1;
   GRANT RESOURCE TO oggadm1;
   GRANT SELECT ANY DICTIONARY TO oggadm1;
   GRANT FLASHBACK ANY TABLE TO oggadm1;
   GRANT SELECT ANY TABLE TO oggadm1;
   GRANT SELECT_CATALOG_ROLE TO rds_master_user_name WITH ADMIN OPTION;
   EXEC rdsadmin.rdsadmin_util.grant_sys_object ('DBA_CLUSTERS', 'OGGADM1');
   GRANT EXECUTE ON DBMS_FLASHBACK TO oggadm1;
   GRANT SELECT ON SYS.V_$DATABASE TO oggadm1;
   GRANT ALTER ANY TABLE TO oggadm1;
   ```

1. Grant the privileges needed by a user account to be an Oracle GoldenGate administrator\. The package that you use to perform the grant, `dbms_goldengate_auth` or `rdsadmin_dbms_goldengate_auth`, depends on the Oracle DB engine version\.
   + For Oracle DB versions that are later than or equal to Oracle Database 12c Release 2 \(12\.2\), which requires patch level 12\.2\.0\.1\.ru\-2019\-04\.rur\-2019\-04\.r1 or later, run the following PL/SQL program\.

     ```
     EXEC rdsadmin.rdsadmin_dbms_goldengate_auth.grant_admin_privilege (
         grantee                 => 'OGGADM1',
         privilege_type          => 'capture',
         grant_select_privileges => true, 
         do_grants               => TRUE);
     ```
   + For Oracle database versions that are earlier than Oracle Database 12c Release 2 \(12\.2\), run the following PL/SQL program\.

     ```
     EXEC dbms_goldengate_auth.grant_admin_privilege (
         grantee                 => 'OGGADM1',
         privilege_type          => 'capture',
         grant_select_privileges => true,
         do_grants               => TRUE);
     ```

   To revoke privileges, use the procedure `revoke_admin_privilege` in the same package\.

#### Step 6: Add a TNS alias for the source database<a name="Appendix.OracleGoldenGate.Source.TNS"></a>

Add the following entry to `$ORACLE_HOME/network/admin/tnsnames.ora` in the Oracle home to be used by the `EXTRACT` process\. For more information on the `tnsnames.ora` file, see the [Oracle documentation](https://docs.oracle.com/en/database/oracle/oracle-database/19/netrf/local-naming-parameters-in-tns-ora-file.html#GUID-7F967CE5-5498-427C-9390-4A5C6767ADAA)\.

```
OGGSOURCE=
   (DESCRIPTION= 
        (ENABLE=BROKEN)
        (ADDRESS_LIST= 
            (ADDRESS=(PROTOCOL=TCP)(HOST=goldengate-source.abcdef12345.us-west-2.rds.amazonaws.com)(PORT=8200)))
        (CONNECT_DATA=(SERVICE_NAME=ORCL))
    )
```

### Setting up a target database for use with Oracle GoldenGate on Amazon RDS<a name="Appendix.OracleGoldenGate.Target"></a>

In this task, you set up a target DB instance for use with Oracle GoldenGate\.

**Topics**
+ [Step 1: Set the ENABLE\_GOLDENGATE\_REPLICATION initialization parameter to true](#Appendix.OracleGoldenGate.Target.enable-gg-rep)
+ [Step 2: Create an Oracle GoldenGate user account on the target database](#Appendix.OracleGoldenGate.Target.User)
+ [Step 3: Grant account privileges on the target database](#Appendix.OracleGoldenGate.Target.Privileges)
+ [Step 4: Add a TNS alias for the target database](#Appendix.OracleGoldenGate.Target.TNS)

#### Step 1: Set the ENABLE\_GOLDENGATE\_REPLICATION initialization parameter to true<a name="Appendix.OracleGoldenGate.Target.enable-gg-rep"></a>

When you set the `ENABLE_GOLDENGATE_REPLICATION` initialization parameter is to `true`, it allows database services to support logical replication\. If your source database is on an Amazon RDS DB instance, make sure that you have a parameter group assigned to the DB instance with the `ENABLE_GOLDENGATE_REPLICATION` initialization parameter set to `true`\. For more information about the `ENABLE_GOLDENGATE_REPLICATION` initialization parameter, see the [Oracle Database documentation](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/ENABLE_GOLDENGATE_REPLICATION.html)\.

#### Step 2: Create an Oracle GoldenGate user account on the target database<a name="Appendix.OracleGoldenGate.Target.User"></a>

Oracle GoldenGate runs as a database user and requires the appropriate database privileges\. To make sure it has these privileges, create a user account on the target database\.

The following statement creates a user named `oggadm1`\.

```
CREATE TABLESPSACE administrator;
CREATE USER oggadm1  IDENTIFIED BY "password" 
   DEFAULT TABLESPACE administrator 
   TEMPORARY TABLESPACE temp;
ALTER USER oggadm1 QUOTA UNLIMITED ON administrator;
```

#### Step 3: Grant account privileges on the target database<a name="Appendix.OracleGoldenGate.Target.Privileges"></a>

In this task, you grant necessary account privileges for database users on your target database\.

**To grant account privileges on the target database**

1. Grant necessary privileges to the Oracle GoldenGate user account on the target database\. In the following example, you grant privileges to `oggadm1`\.

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

1. Grant the privileges needed by a user account to be an Oracle GoldenGate administrator\. The package that you use to perform the grant, `dbms_goldengate_auth` or `rdsadmin_dbms_goldengate_auth`, depends on the Oracle DB engine version\.
   + For Oracle database versions that are later than or equal to Oracle Database 12c Release 2 \(12\.2\), which requires patch level 12\.2\.0\.1\.ru\-2019\-04\.rur\-2019\-04\.r1 or later, run the following PL/SQL program\.

     ```
     EXEC rdsadmin.rdsadmin_dbms_goldengate_auth.grant_admin_privilege (
         grantee                 => 'OGGADM1',
         privilege_type          => 'apply',
         grant_select_privileges => true, 
         do_grants               => TRUE);
     ```
   + For Oracle database versions that are lower than Oracle Database 12c Release 2 \(12\.2\), run the following PL/SQL program\.

     ```
     EXEC dbms_goldengate_auth.grant_admin_privilege (
         grantee                 => 'OGGADM1',
         privilege_type          => 'apply',
         grant_select_privileges => true, 
         do_grants               => TRUE);
     ```

   To revoke privileges, use the procedure `revoke_admin_privilege` in the same package\.

#### Step 4: Add a TNS alias for the target database<a name="Appendix.OracleGoldenGate.Target.TNS"></a>

Add the following entry to `$ORACLE_HOME/network/admin/tnsnames.ora` in the Oracle home to be used by the `REPLICAT` process\. For Oracle Multitenant databases, make sure that the TNS alias points to the service name of the PDB\. For more information on the `tnsnames.ora` file, see the [Oracle documentation](https://docs.oracle.com/en/database/oracle/oracle-database/19/netrf/local-naming-parameters-in-tns-ora-file.html#GUID-7F967CE5-5498-427C-9390-4A5C6767ADAA)\.

```
OGGTARGET=
    (DESCRIPTION= 
        (ENABLE=BROKEN)
        (ADDRESS_LIST= 
            (ADDRESS=(PROTOCOL=TCP)(HOST=goldengate-target.abcdef12345.us-west-2.rds.amazonaws.com)(PORT=8200)))
        (CONNECT_DATA=(SERVICE_NAME=ORCL))
    )
```

## Working with the EXTRACT and REPLICAT utilities of Oracle GoldenGate<a name="Appendix.OracleGoldenGate.ExtractReplicat"></a>

The Oracle GoldenGate utilities `EXTRACT` and `REPLICAT` work together to keep the source and target databases in sync via incremental transaction replication using trail files\. All changes that occur on the source database are automatically detected by `EXTRACT`, then formatted and transferred to trail files on the Oracle GoldenGate on\-premises or Amazon EC2 instance hub\. After initial load is completed, the data is read from these files and replicated to the target database by the `REPLICAT` utility\.

### Running the Oracle GoldenGate EXTRACT utility<a name="Appendix.OracleGoldenGate.Extract"></a>

The `EXTRACT` utility retrieves, converts, and outputs data from the source database to trail files\. The basic process is as follows:

1. `EXTRACT` queues transaction details to memory or to temporary disk storage\.

1. The source database commits the transaction\.

1. `EXTRACT` writes the transaction details to a trail file\.

1. The trail file routes these details to the Oracle GoldenGate on\-premises or the Amazon EC2 instance hub and then to the target database\.

The following steps start the `EXTRACT` utility, capture the data from `EXAMPLE.TABLE` in source database `OGGSOURCE`, and create the trail files\. 

**To run the EXTRACT utility**

1. Configure the `EXTRACT` parameter file on the Oracle GoldenGate hub \(on\-premises or Amazon EC2 instance\)\. The following listing shows an example `EXTRACT` parameter file named `$GGHOME/dirprm/eabc.prm`\.

   ```
   EXTRACT EABC
    
   USERID oggadm1@OGGSOURCE, PASSWORD "my-password"
   EXTTRAIL /path/to/goldengate/dirdat/ab 
    
   IGNOREREPLICATES
   GETAPPLOPS
   TRANLOGOPTIONS EXCLUDEUSER OGGADM1
   	 
   TABLE EXAMPLE.TABLE;
   ```

1. On the Oracle GoldenGate hub, log in to the source database and launch the Oracle GoldenGate command line interface `ggsci`\. The following example shows the format for logging in\.

   ```
   dblogin oggadm1@OGGSOURCE
   ```

1. Add transaction data to turn on supplemental logging for the database table\.

   ```
   add trandata EXAMPLE.TABLE
   ```

1. Using the `ggsci` command line, enable the `EXTRACT` utility using the following commands\.

   ```
   add extract EABC tranlog, INTEGRATED tranlog, begin now
   add exttrail /path/to/goldengate/dirdat/ab 
      extract EABC, 
      MEGABYTES 100
   ```

1. Register the `EXTRACT` utility with the database so that the archive logs are not deleted\. This task allows you to recover old, uncommitted transactions if necessary\. To register the `EXTRACT` utility with the database, use the following command\.

   ```
   register EXTRACT EABC, DATABASE
   ```

1. Start the `EXTRACT` utility with the following command\.

   ```
   start EABC
   ```

### Running the Oracle GoldenGate REPLICAT utility<a name="Appendix.OracleGoldenGate.Replicat"></a>

The `REPLICAT` utility "pushes" transaction information in the trail files to the target database\.

The following steps enable and start the `REPLICAT` utility so that it can replicate the captured data to the table `EXAMPLE.TABLE` in target database `OGGTARGET`\.

**To run the REPLICATE utility**

1. Configure the `REPLICAT` parameter file on the Oracle GoldenGate hub \(on\-premises or EC2 instance\)\. The following listing shows an example `REPLICAT` parameter file named `$GGHOME/dirprm/rabc.prm`\.

   ```
   REPLICAT RABC
    
   USERID oggadm1@OGGTARGET, password "my-password"
    
   ASSUMETARGETDEFS
   MAP EXAMPLE.TABLE, TARGET EXAMPLE.TABLE;
   ```

1. Log in to the target database and launch the Oracle GoldenGate command line interface \(`ggsci`\)\. The following example shows the format for logging in\.

   ```
   dblogin userid oggadm1@OGGTARGET
   ```

1. Using the `ggsci` command line, add a checkpoint table\. The user indicated should be the Oracle GoldenGate user account, not the target table schema owner\. The following example creates a checkpoint table named `gg_checkpoint`\.

   ```
   add checkpointtable oggadm1.oggchkpt
   ```

1. To enable the `REPLICAT` utility, use the following command\.

   ```
   add replicat RABC EXTTRAIL /path/to/goldengate/dirdat/ab CHECKPOINTTABLE oggadm1.oggchkpt 
   ```

1. Start the `REPLICAT` utility by using the following command\.

   ```
   start RABC
   ```

## Monitoring Oracle GoldenGate<a name="Appendix.OracleGoldenGate.Monitoring"></a>

When you use Oracle GoldenGate for replication, make sure that the Oracle GoldenGate process is up and running and the source and target databases are synchronized\. You can use the following monitoring tools:
+ [Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) is a monitoring service that is used in this pattern to monitor GoldenGate error logs\.
+ [Amazon SNS](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/US_SetupSNS.html) is a message notification service that is used in this pattern to send email notifications\.

For detailed instructions, see [Monitor Oracle GoldenGate logs by using Amazon CloudWatch](https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/monitor-oracle-goldengate-logs-by-using-amazon-cloudwatch.html)\.

## Troubleshooting Oracle GoldenGate<a name="Appendix.OracleGoldenGate.Troubleshooting"></a>

This section explains the most common issues when using Oracle GoldenGate with Amazon RDS for Oracle\.

**Topics**
+ [Error opening an online redo log](#Appendix.OracleGoldenGate.Troubleshooting.Logs)
+ [Oracle GoldenGate appears to be properly configured but replication is not working](#Appendix.OracleGoldenGate.Troubleshooting.Replication)
+ [Integrated REPLICAT slow due to query on SYS\."\_DBA\_APPLY\_CDR\_INFO"](#Appendix.OracleGoldenGate.IR)

### Error opening an online redo log<a name="Appendix.OracleGoldenGate.Troubleshooting.Logs"></a>

Make sure that you configure your databases to retain archived redo logs\. Consider the following guidelines:
+ Specify the duration for log retention in hours\. The minimum value is one hour\.
+ Set the duration to exceed any potential downtime of the source DB instance, any potential period of communication, and any potential period of networking issues for the source DB instance\. Such a duration lets Oracle GoldenGate recover logs from the source DB instance as needed\.
+ Ensure that you have sufficient storage on your instance for the files\.

If you don't have log retention enabled, or if the retention value is too small, you receive an error message similar to the following\.

```
2022-03-06 06:17:27  ERROR   OGG-00446  error 2 (No such file or directory) 
opening redo log /rdsdbdata/db/GGTEST3_A/onlinelog/o1_mf_2_9k4bp1n6_.log for sequence 1306 
Not able to establish initial position for begin time 2022-03-06 06:16:55.
```

### Oracle GoldenGate appears to be properly configured but replication is not working<a name="Appendix.OracleGoldenGate.Troubleshooting.Replication"></a>

For pre\-existing tables, you must specify the SCN that Oracle GoldenGate works from\.

**To fix this issue**

1. Log in to the source database and launch the Oracle GoldenGate command line interface \(`ggsci`\)\. The following example shows the format for logging in\.

   ```
   dblogin userid oggadm1@OGGSOURCE
   ```

1. Using the `ggsci` command line, set up the start SCN for the `EXTRACT` process\. The following example sets the SCN to 223274 for the `EXTRACT`\.

   ```
   ALTER EXTRACT EABC SCN 223274
   start EABC
   ```

1. Log in to the target database\. The following example shows the format for logging in\.

   ```
   dblogin userid oggadm1@OGGTARGET
   ```

1. Using the `ggsci` command line, set up the start SCN for the `REPLICAT` process\. The following example sets the SCN to 223274 for the `REPLICAT`\.

   ```
   start RABC atcsn 223274
   ```

### Integrated REPLICAT slow due to query on SYS\."\_DBA\_APPLY\_CDR\_INFO"<a name="Appendix.OracleGoldenGate.IR"></a>

Oracle GoldenGate Conflict Detection and Resolution \(CDR\) provides basic conflict resolution routines\. For example, CDR can resolve a unique conflict for an `INSERT` statement\.

When CDR resolves a collision, it can insert records into the exception table `_DBA_APPLY_CDR_INFO` temporarily\. Integrated `REPLICAT` deletes these records later\. In a rare scenario, the integrated `REPLICAT` can process a large number of collisions, but a new integrated `REPLICAT` does not replace it\. Instead of being removed, the existing rows in `_DBA_APPLY_CDR_INFO` are orphaned\. Any new integrated `REPLICAT` processes slow down because they are querying orphaned rows in `_DBA_APPLY_CDR_INFO`\.

To remove all rows from `_DBA_APPLY_CDR_INFO`, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.truncate_apply$_cdr_info`\. This procedure is released as part of the October 2020 release and patch update\. The procedure is available in the following database versions:
+ [ Version 21\.0\.0\.0\.ru\-2022\-01\.rur\-2022\-01\.r1](https://docs.aws.amazon.com/AmazonRDS/latest/OracleReleaseNotes/oracle-version-21-0.html#oracle-version-RU-RUR.21.0.0.0.ru-2022-01.rur-2022-01.r1) and higher
+ [ Version 19\.0\.0\.0\.ru\-2020\-10\.rur\-2020\-10\.r1](https://docs.aws.amazon.com/AmazonRDS/latest/OracleReleaseNotes/oracle-version-19-0.html#oracle-version-RU-RUR.19.0.0.0.ru-2020-10.rur-2020-10.r1) and higher

The following example truncates the table `_DBA_APPLY_CDR_INFO`\.

```
SET SERVEROUTPUT ON SIZE 2000
EXEC rdsadmin.rdsadmin_util.truncate_apply$_cdr_info;
```