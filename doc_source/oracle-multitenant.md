# Working with CDBs in RDS for Oracle<a name="oracle-multitenant"></a>

In the Oracle multitenant architecture, a container database \(CDB\) can include customer\-created pluggable databases \(PDBs\)\. For more information about CDBs, see [Introduction to the Multitenant Architecture ](https://docs.oracle.com/en/database/oracle/oracle-database/19/multi/introduction-to-the-multitenant-architecture.html#GUID-267F7D12-D33F-4AC9-AA45-E9CD671B6F22) in the Oracle Database documentation\.

**Topics**
+ [Overview of RDS for Oracle CDBs](#Oracle.Concepts.single-tenant)
+ [Configuring an RDS for Oracle CDB](#oracle-cdb.configuring)
+ [Backing up and restoring a CDB](#Oracle.Concepts.single-tenant.snapshots)
+ [Converting an RDS for Oracle non\-CDB to a CDB](#oracle-cdb-converting)
+ [Upgrading your CDB](#Oracle.Concepts.single-tenant.upgrades)

## Overview of RDS for Oracle CDBs<a name="Oracle.Concepts.single-tenant"></a>

You can create an RDS for Oracle DB instance as a container database \(CDB\) when you run Oracle Database 19c or higher\. A CDB differs from a non\-CDB because it can contain pluggable databases \(PDBs\)\. A PDB is a portable collection of schemas and objects that appears to an application as a separate database\.

Starting with Oracle Database 21c, all databases are CDBs\. If your DB instance runs Oracle Database 19c, you can create either a CDB or a non\-CDB\. A non\-CDB uses the traditional Oracle database architecture and can't contain PDBs\. You can convert an Oracle Database 19c non\-CDB to a CDB, but you can't convert a CDB to a non\-CDB\. You can only upgrade a CDB to a CDB\.

**Topics**
+ [Single\-tenant configuration](#single-tenant-access)
+ [Creation and conversion options in a CDB](#oracle-cdb-creation-conversion)
+ [User accounts and privileges in a CDB](#Oracle.Concepts.single-tenant.users)
+ [Parameter group families in a CDB](#Oracle.Concepts.single-tenant.parameters)
+ [PDB portability in a CDB](#Oracle.Concepts.single-tenant.migration)

### Single\-tenant configuration<a name="single-tenant-access"></a>

RDS for Oracle supports the single\-tenant configuration of the Oracle multitenant architecture\. This means that an RDS for Oracle DB instance can contain only one PDB\. You name the PDB when you create your DB instance\. The CDB name defaults to `RDSCDB` and can't be changed\.

In RDS for Oracle, your client application interacts with the PDB rather than the CDB\. Your experience with a PDB is mostly identical to your experience with a non\-CDB\. You use the same Amazon RDS APIs in the single\-tenant configuration as you do in the non\-CDB architecture\. You can't access the CDB itself\.

### Creation and conversion options in a CDB<a name="oracle-cdb-creation-conversion"></a>

Although Oracle Database 21c supports only CDBs, Oracle Database 19c supports both CDBs and non\-CDBs\. The following table shows the different options for creating and converting CDBs and non\-CDBs\.


| Release | Database creation options | Architecture conversion options | Major version upgrade targets | 
| --- | --- | --- | --- | 
| Oracle Database 21c | CDB only | N/A | N/A | 
| Oracle Database 19c | CDB or non\-CDB | Non\-CDB to CDB \(April 2021 RU or higher\) | 21c CDB \(from 19c CDB only\) | 
| Oracle Database 12c \(desupported\) | Non\-CDB only | N/A | 19c non\-CDB | 

As shown in the preceding table, you can't directly upgrade a non\-CDB to a CDB in a new major version\. But you can convert an Oracle Database 19c non\-CDB to an Oracle Database 19c CDB, and then upgrade the Oracle Database 19c CDB to an Oracle Database 21c CDB\. For more information, see [Converting an RDS for Oracle non\-CDB to a CDB](#oracle-cdb-converting)\.

### User accounts and privileges in a CDB<a name="Oracle.Concepts.single-tenant.users"></a>

In the Oracle multitenant architecture, all user accounts are either common users or local users\. A CDB common user is a database user whose single identity and password are known in the CDB root and in every existing and future PDB\. In contrast, a local user exists only in a single PDB\.

The RDS master user is a local user account in the PDB, which you name when you create your DB instance\. If you create new user accounts, these users will also be local users residing in the PDB\. You can't use any user accounts to create new PDBs or modify the state of the existing PDB\.

The `rdsadmin` user is a common user account\. You can run RDS for Oracle packages that exist in this account, but you can't log in as `rdsadmin`\. For more information, see [About Common Users and Local Users](https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/managing-security-for-oracle-database-users.html#GUID-BBBD9904-F2F3-442B-9AFC-8ACDD9A588D8) in the Oracle documentation\.

### Parameter group families in a CDB<a name="Oracle.Concepts.single-tenant.parameters"></a>

CDBs have their own parameter group families and default parameter values\. The CDB parameter group families are as follows:
+ oracle\-ee\-cdb\-21
+ oracle\-se2\-cdb\-21
+ oracle\-ee\-cdb\-19
+ oracle\-se2\-cdb\-19

You specify parameters at the CDB level rather than the PDB level\. The PDB inherits parameter settings from the CDB\. For more information about setting parameters, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. For best practices, see [Working with DB parameter groups](CHAP_BestPractices.md#CHAP_BestPractices.DBParameterGroup)\.

### PDB portability in a CDB<a name="Oracle.Concepts.single-tenant.migration"></a>

Your CDB can contain only a single PDB\. You can't unplug this PDB or plug in a different PDB\. To move data into or out of your CDB, use the same techniques as for a non\-CDB\. For more information about migrating data, see [Importing data into Oracle on Amazon RDS](Oracle.Procedural.Importing.md)\.

## Configuring an RDS for Oracle CDB<a name="oracle-cdb.configuring"></a>

Configuring a CDB is similar to configuring a non\-CDB\. 

**Topics**
+ [Creating an RDS for Oracle CDB instance](#Oracle.Concepts.single-tenant.creation)
+ [Connecting to a PDB in your RDS for Oracle CDB](#Oracle.Concepts.single-tenant.users)

### Creating an RDS for Oracle CDB instance<a name="Oracle.Concepts.single-tenant.creation"></a>

In RDS for Oracle, creating a CDB is almost identical to creating a non\-CDB\. The difference is that you choose the Multitenant architecture when creating your DB instance\. To create a CDB, use the AWS Management Console, the AWS CLI, or the RDS API\.

#### Console<a name="Oracle.Concepts.single-tenant.creation.console"></a>

**To create a CDB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region in which you want to create the CDB instance\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**\.

1. In **Choose a database creation method**, select **Standard Create**\.

1. In **Engine options**, choose **Oracle**\. 

1. For **Database management type**, choose **Amazon RDS**\.

1. For **Architecture settings**, choose **Multitenant architecture**\. 

1. Choose the settings that you want based on the options listed in [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\. Note the following:
   + For **Master username**, enter the name for a local user in your PDB\. You can't use the master username to log in to the CDB root\.
   + For **Initial database name**, enter the name of your PDB\. You can't name the CDB, which has the default name `RDSCDB`\.

1. Choose **Create database**\.

#### AWS CLI<a name="Oracle.Concepts.single-tenant.creation.cli"></a>

To create a DB instance by using the AWS CLI, call the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command with the following parameters:
+ `--db-instance-identifier`
+ `--db-instance-class`
+ `--engine { oracle-ee-cdb | oracle-se2-cdb }`
+ `--master-username`
+ `--master-user-password`
+ `--allocated-storage`
+ `--backup-retention-period`

For information about each setting, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\.

This following example creates an RDS for Oracle DB instance named *my\-cdb\-inst*\. The PDB is named *mypdb*\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds create-db-instance \
2.     --engine oracle-ee-cdb \
3.     --db-instance-identifier my-cdb-inst \
4.     --db-name mypdb \
5.     --allocated-storage 250 \
6.     --db-instance-class db.t3.large \
7.     --master-username pdb_admin \
8.     --master-user-password masteruserpassword \
9.     --backup-retention-period 3
```
For Windows:  

```
1. aws rds create-db-instance ^
2.     --engine oracle-ee-cdb ^
3.     --db-instance-identifier my-cdb-inst ^
4.     --db-name mypdb ^
5.     --allocated-storage 250 ^
6.     --db-instance-class db.t3.large ^
7.     --master-username pdb_admin ^
8.     --master-user-password masteruserpassword ^
9.     --backup-retention-period 3
```
This command produces output similar to the following\.   

```
 1. {
 2.     "DBInstance": {
 3.         "DBInstanceIdentifier": "my-cdb-inst",
 4.         "DBInstanceClass": "db.t3.large",
 5.         "Engine": "oracle-ee-cdb",
 6.         "DBInstanceStatus": "creating",
 7.         "MasterUsername": "pdb_user",
 8.         "DBName": "MYPDB",
 9.         "AllocatedStorage": 250,
10.         "PreferredBackupWindow": "04:59-05:29",
11.         "BackupRetentionPeriod": 3,
12.         "DBSecurityGroups": [],
13.         "VpcSecurityGroups": [
14.             {
15.                 "VpcSecurityGroupId": "sg-0a2bcd3e",
16.                 "Status": "active"
17.             }
18.         ],
19.         "DBParameterGroups": [
20.             {
21.                 "DBParameterGroupName": "default.oracle-ee-cdb-19",
22.                 "ParameterApplyStatus": "in-sync"
23.             }
24.         ],
25.         "DBSubnetGroup": {
26.             "DBSubnetGroupName": "default",
27.             "DBSubnetGroupDescription": "default",
28.             "VpcId": "vpc-1234567a",
29.             "SubnetGroupStatus": "Complete",
30.             ...
```

#### RDS API<a name="Oracle.Concepts.single-tenant.creation.api"></a>

To create a DB instance by using the Amazon RDS API, call the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) operation\.

For information about each setting, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\. 

### Connecting to a PDB in your RDS for Oracle CDB<a name="Oracle.Concepts.single-tenant.users"></a>

You can use a utility like SQL\*Plus to connect to a PDB\. To download Oracle Instant Client, which includes a standalone version of SQL\*Plus, see [ Oracle Instant Client Downloads](https://www.oracle.com/database/technologies/instant-client/downloads.html)\.

To connect SQL\*Plus to your PDB, you need the following information:
+ Database user name and password
+ Endpoint for your DB instance
+ Port number

For information about finding the preceding information, see [Finding the endpoint of your Oracle DB instance](USER_Endpoint.md)\.

**Example To connect to your PDB using SQL\*Plus**  
In the following examples, substitute your master user for *master\_user\_name*\. Also, substitute the endpoint for your DB instance, and then include the port number and the Oracle SID\. The SID value is the name of the PDB that you specified when you created your DB instance, and not the DB instance identifier\.  
For Linux, macOS, or Unix:  

```
1. sqlplus 'master_user_name@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=endpoint)(PORT=port))(CONNECT_DATA=(SID=pdb_name)))'
```
For Windows:  

```
1. sqlplus master_user_name@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=endpoint)(PORT=port))(CONNECT_DATA=(SID=pdb_name)))
```
You should see output similar to the following\.  

```
SQL*Plus: Release 19.0.0.0.0 Production on Mon Aug 21 09:42:20 2021
```
After you enter the password for the user, the SQL prompt appears\.  

```
SQL>
```

**Note**  
The shorter format connection string \(Easy connect or EZCONNECT\), such as `sqlplus username/password@LONGER-THAN-63-CHARS-RDS-ENDPOINT-HERE:1521/database-identifier`, might encounter a maximum character limit and should not be used to connect\. 

## Backing up and restoring a CDB<a name="Oracle.Concepts.single-tenant.snapshots"></a>

DB snapshots work the same way in the multitenant and non\-multitenant architecture\. The only difference is that when you restore a DB snapshot, you can only rename the PDB, not the CDB\. The CDB is always named `RDSCDB`\. For more information, see [Oracle Database considerations](USER_RestoreFromSnapshot.md#USER_RestoreFromSnapshot.Oracle)\.

## Converting an RDS for Oracle non\-CDB to a CDB<a name="oracle-cdb-converting"></a>

You can change the architecture of an Oracle database from the traditional non\-CDB architecture to the multitenant architecture\. When you upgrade your database engine version, you can't change the database architecture in the same operation\. Therefore, to upgrade an Oracle Database 19c non\-CDB to an Oracle Database 21c CDB, you first need to convert the non\-CDB to a CDB, and then upgrade the 19c CDB to a 21c CDB\.

The non\-CDB conversion operation has the following requirements:
+ Make sure that you specify `oracle-ee-cdb` or `oracle-se2-cdb` for the engine type\. These are the only supported values\.
+ Make sure that your DB engine runs Oracle Database 19c with an April 2021 or later RU\.

The operation has the following limitations:
+ You can't convert a CDB to a non\-CDB\. You can only convert a non\-CDB to a CDB\.
+ You can't convert a primary or replica database that has Oracle Data Guard enabled\.
+ You can't upgrade the DB engine version and convert a non\-CDB to a CDB in the same operation\.
+ The considerations for option and parameter groups are the same as for upgrading the DB engine\. For more information, see [Considerations for Oracle DB upgrades](USER_UpgradeDBInstance.Oracle.OGPG.md)\.

### Console<a name="oracle-cdb-converting.console"></a>

**To convert a non\-CDB to a CDB**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region where your DB instance resides\.

1. In the navigation pane, choose **Databases**, and then choose the non\-CDB instance that you want to convert to a CDB instance\. 

1. Choose **Modify**\.

1. For **Architecture settings**, select **Multitenant architecture**\.

1. \(Optional\) For **DB parameter group**, choose a new parameter group for your CDB instance\. The same parameter group considerations apply when converting a DB instance as when upgrading a DB instance\. For more information, see [Parameter group considerations](USER_UpgradeDBInstance.Oracle.OGPG.md#USER_UpgradeDBInstance.Oracle.OGPG.PG)\.

1. \(Optional\) For **Option group**, choose a new option group for your CDB instance\. The same option group considerations apply when converting a DB instance as when upgrading a DB instance\. For more information, see [Option group considerations](USER_UpgradeDBInstance.Oracle.OGPG.md#USER_UpgradeDBInstance.Oracle.OGPG.OG)\.

1. When all the changes are as you want them, choose **Continue** and check the summary of modifications\. 

1. \(Optional\) Choose **Apply immediately** to apply the changes immediately\. Choosing this option can cause downtime in some cases\. For more information, see [Using the Apply Immediately setting](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\.

1. On the confirmation page, review your changes\. If they are correct, choose **Modify DB instance**\.

   Or choose **Back** to edit your changes or **Cancel** to cancel your changes\.

### AWS CLI<a name="oracle-cdb-converting.cli"></a>

To convert the non\-CDB on your DB instance to a CDB, set `--engine` to `oracle-ee-cdb` or `oracle-se2-cdb` in the AWS CLI command [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)\.

The following example converts the DB instance named *my\-non\-cdb* and specifies a custom option group and parameter group\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier my-non-cdb \
    --engine oracle-ee-cdb \
    --option-group-name custom-option-group \
    --db-parameter-group-name custom-parameter-group
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier my-non-cdb ^
    --engine oracle-ee-cdb ^
    --option-group-name custom-option-group ^
    --db-parameter-group-name custom-parameter-group
```

### RDS API<a name="oracle-cdb-converting.api"></a>

To convert a non\-CDB to a CDB, specify `Engine` in the RDS API operation [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html)\.

## Upgrading your CDB<a name="Oracle.Concepts.single-tenant.upgrades"></a>

You can upgrade a CDB to a different Oracle Database release\. For example, you can upgrade an Oracle Database 19c CDB to an Oracle Database 21c CDB\. You can't change the database architecture during an upgrade\. Thus, you can't upgrade a non\-CDB to a CDB or upgrade a CDB to a non\-CDB\.

The procedure for upgrading a CDB to a CDB is the same as for upgrading a non\-CDB to a non\-CDB\. For more information, see [Upgrading the RDS for Oracle DB engine](USER_UpgradeDBInstance.Oracle.md)\.