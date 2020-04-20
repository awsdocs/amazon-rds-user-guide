# Importing Data into Oracle on Amazon RDS<a name="Oracle.Procedural.Importing"></a>

How you import data into an Amazon RDS DB instance depends on the amount of data you have and the number and variety of database objects in your database\. For example, you can use Oracle SQL Developer to import a simple, 20 MB database\. You can use Oracle Data Pump to import complex databases, or databases that are several hundred megabytes or several terabytes in size\. 

You can also use AWS Database Migration Service \(AWS DMS\) to import data into an Amazon RDS DB instance\. AWS DMS can migrate databases without downtime and, for many database engines, continue ongoing replication until you are ready to switch over to the target database\. You can migrate to Oracle from either the same database engine or a different database engine using AWS DMS\. If you are migrating from a different database engine, you can use the AWS Schema Conversion Tool to migrate schema objects that are not migrated by AWS DMS\. For more information about AWS DMS, see [ What is AWS Database Migration Service](https://docs.aws.amazon.com/dms/latest/userguide/Welcome.html)\. 

Before you use any of these migration techniques, we recommend the best practice of taking a backup of your database\. After you import the data, you can back up your Amazon RDS DB instances by creating snapshots\. Later, you can restore the database from the snapshots\. For more information, see [Backing Up and Restoring an Amazon RDS DB Instance](CHAP_CommonTasks.BackupRestore.md)\. 

**Note**  
You can also import data into Oracle using files from Amazon S3\. For example, you can download Data Pump files from Amazon S3 to the DB instance host\. For more information, see [Amazon S3 Integration](oracle-s3-integration.md)\.

## Importing Using Oracle SQL Developer<a name="Oracle.Procedural.Importing.SQLDeveloper"></a>

For small databases, you can use Oracle SQL Developer, a graphical Java tool distributed without cost by Oracle\. You can install this tool on your desktop computer \(Windows, Linux, or Mac\) or on one of your servers\. Oracle SQL Developer provides options for migrating data between two Oracle databases, or for migrating data from other databases, such as MySQL, to Oracle\. Oracle SQL Developer is best suited for migrating small databases\. We recommend that you read the Oracle SQL Developer product documentation before you begin migrating your data\. 

After you install SQL Developer, you can use it to connect to your source and target databases\. Use the **Database Copy** command on the Tools menu to copy your data to your Amazon RDS instance\. 

To download Oracle SQL Developer, go to [http://www\.oracle\.com/technetwork/developer\-tools/sql\-developer](http://www.oracle.com/technetwork/developer-tools/sql-developer)\. 

Oracle also has documentation on how to migrate from other databases, including MySQL and SQL Server\. For more information, see [http://www\.oracle\.com/technetwork/database/migration](http://www.oracle.com/technetwork/database/migration) in the Oracle documentation\. 

## Importing Using Oracle Data Pump<a name="Oracle.Procedural.Importing.DataPump"></a>

Oracle Data Pump is a long\-term replacement for the Oracle Export/Import utilities\. Oracle Data Pump is the preferred way to move large amounts of data from an Oracle installation to an Amazon RDS DB instance\. You can use Oracle Data Pump for several scenarios: 
+ Import data from an Oracle database \(either on\-premises or Amazon EC2 instance\) to an Amazon RDS for Oracle DB instance\.
+ Import data from an RDS Oracle DB instance to an Oracle database \(either on\-premises or Amazon EC2 instance\)\.
+ Import data between RDS Oracle DB instances \(for example, to migrate data from EC2\-Classic to VPC\)\.

To download Oracle Data Pump utilities, see [Oracle Database Software Downloads](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html) on the Oracle Technology Network website\. 

For compatibility considerations when migrating between versions of Oracle Database, see [ the Oracle documentation](https://docs.oracle.com/en/database/oracle/oracle-database/19/sutil/oracle-data-pump-overview.html#GUID-BAA3B679-A758-4D55-9820-432D9EB83C68)\. 

When you import data with Oracle Data Pump, you must transfer the dump file that contains the data from the source database to the target database\. You can transfer the dump file using an Amazon S3 bucket or by using a database link between the two databases\.

The following are best practices for using Oracle Data Pump to import data into an Amazon RDS for Oracle DB instance:
+ Perform imports in `schema` or `table` mode to import specific schemas and objects\.
+ Limit the schemas you import to those required by your application\.
+ Do not import in `full` mode\.

  Because Amazon RDS for Oracle does not allow access to `SYS` or `SYSDBA` administrative users, importing in `full` mode, or importing schemas for Oracle\-maintained components, might damage the Oracle data dictionary and affect the stability of your database\.
+ When loading large amounts of data, transfer the dump file to the target Amazon RDS for Oracle DB instance, take a DB snapshot of your instance, and then test the import to verify that it succeeds\. If database components are invalidated, you can delete the DB instance and re\-create it from the DB snapshot\. The restored DB instance includes any dump files staged on the DB instance when you took the DB snapshot\.
+ Do not import dump files that were created using the Oracle Data Pump export parameters `TRANSPORT_TABLESPACES`, `TRANSPORTABLE`, or `TRANSPORT_FULL_CHECK`\. Amazon RDS for Oracle DB instances do not support importing these dump files\.

The examples in this section show one way to import data into an Oracle database, but there are many ways to do so with Oracle Data Pump\. For complete information about using Oracle Data Pump, see [ the Oracle documentation](https://docs.oracle.com/en/database/oracle/oracle-database/19/sutil/oracle-data-pump.html#GUID-501A9908-BCC5-434C-8853-9A6096766B5A)\.

The examples in this section use the `DBMS_DATAPUMP` package\. The same tasks can be accomplished by using the Oracle Data Pump command line utilities `impdp` and `expdp`\. You can install these utilities on a remote host as part of an Oracle Client installation, including Oracle Instant Client\.

**Topics**
+ [Importing Data with Oracle Data Pump and an Amazon S3 Bucket](#Oracle.Procedural.Importing.DataPump.S3)
+ [Importing Data with Oracle Data Pump and a Database Link](#Oracle.Procedural.Importing.DataPump.DBLink)

### Importing Data with Oracle Data Pump and an Amazon S3 Bucket<a name="Oracle.Procedural.Importing.DataPump.S3"></a>

The following import process uses Oracle Data Pump and an Amazon S3 bucket\. The process exports data on the source database using the Oracle [DBMS\_DATAPUMP](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_DATAPUMP.html) package and puts the dump file in an Amazon S3 bucket\. It then downloads the dump file from the Amazon S3 bucket to the DATA\_PUMP\_DIR directory on the target Amazon RDS Oracle DB instance\. The final step imports the data from the copied dump file into the Amazon RDS Oracle DB instance using the DBMS\_DATAPUMP package\. 

The process has the following requirements:
+ You must have an Amazon S3 bucket available for file transfers, and the Amazon S3 bucket must be in the same AWS Region as the DB instance\. For instructions, see [Create a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\.
+ You must prepare the Amazon S3 bucket for Amazon RDS integration by following the instructions in [Prerequisites for Amazon RDS Oracle Integration with Amazon S3](oracle-s3-integration.md#oracle-s3-integration.preparing)\.
+ You must ensure that you have enough storage space to store the dump file on the source instance and the target DB instance\.

**Note**  
This process imports a dump file into the DATA\_PUMP\_DIR directory, a preconfigured directory on all Oracle DB instances\. This directory is located on the same storage volume as your data files\. When you import the dump file, the existing Oracle data files use more space\. Thus, you should make sure that your DB instance can accommodate that additional use of space\. The imported dump file is not automatically deleted or purged from the DATA\_PUMP\_DIR directory\. To remove the imported dump file, use [UTL\_FILE\.FREMOVE](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/UTL_FILE.html#GUID-09B09C2A-2C21-4F70-BF04-D0EEA7B59CAF), found on the Oracle website\. 

The import process using Oracle Data Pump and an Amazon S3 bucket has the following steps\.

**Topics**
+ [Step 1: Grant Privileges to the User on the Amazon RDS Target Instance](#Oracle.Procedural.Importing.DataPumpS3.Step1)
+ [Step 2: Use DBMS\_DATAPUMP to Create a Dump File](#Oracle.Procedural.Importing.DataPumpS3.Step2)
+ [Step 3: Upload the Dump File to Your Amazon S3 Bucket](#Oracle.Procedural.Importing.DataPumpS3.Step3)
+ [Step 4: Copy the Exported Dump File from the Amazon S3 Bucket to the Target DB Instance](#Oracle.Procedural.Importing.DataPumpS3.Step4)
+ [Step 5: Use DBMS\_DATAPUMP to Import the Data File on the Target DB Instance](#Oracle.Procedural.Importing.DataPumpS3.Step5)
+ [Step 6: Clean Up](#Oracle.Procedural.Importing.DataPumpS3.Step6)

#### Step 1: Grant Privileges to the User on the Amazon RDS Target Instance<a name="Oracle.Procedural.Importing.DataPumpS3.Step1"></a>

To grant privileges to the user on the RDS target instance, take the following steps:

1. Use SQL Plus or Oracle SQL Developer to connect to the Amazon RDS target Oracle DB instance into which the data will be imported\. Connect as the Amazon RDS master user\. For information about connecting to the DB instance, see [Connecting to a DB Instance Running the Oracle Database Engine](USER_ConnectToOracleInstance.md)\.

1. Create the required tablespaces before you import the data\. For more information, see [Creating and Sizing Tablespaces](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.CreatingTablespacesAndDatafiles)\.

1. If the user account into which the data is imported doesn't exist, create the user account and grant the necessary permissions and roles\. If you plan to import data into multiple user schemas, create each user account and grant the necessary privileges and roles to it\.

   For example, the following commands create a new user and grant the necessary permissions and roles to import the data into the user's schema\.

   ```
   create user schema_1 identified by <password>;
   grant create session, resource to schema_1;
   alter user schema_1 quota 100M on users;
   ```

   This example grants the new user the `CREATE SESSION` privilege and the `RESOURCE` role\. Additional privileges and roles might be required depending on the database objects that you import\. 
**Note**  
Replace `schema_1` with the name of your schema in this step and in the following steps\.

#### Step 2: Use DBMS\_DATAPUMP to Create a Dump File<a name="Oracle.Procedural.Importing.DataPumpS3.Step2"></a>

 Use SQL Plus or Oracle SQL Developer to connect to the source Oracle instance with an administrative user\. If the source database is an Amazon RDS Oracle DB instance, connect with the Amazon RDS master user\. Next, use the Oracle Data Pump utility to create a dump file\. 

The following script creates a dump file named *sample\.dmp* in the DATA\_PUMP\_DIR directory that contains the `SCHEMA_1` schema\. Replace `SCHEMA_1` with the name of the schema you want to export\. 

```
DECLARE
hdnl NUMBER;
BEGIN
hdnl := DBMS_DATAPUMP.OPEN( operation => 'EXPORT', job_mode => 'SCHEMA', job_name=>null);
DBMS_DATAPUMP.ADD_FILE( handle => hdnl, filename => 'sample.dmp', directory => 'DATA_PUMP_DIR', filetype => dbms_datapump.ku$_file_type_dump_file);
DBMS_DATAPUMP.ADD_FILE( handle => hdnl, filename => 'sample_exp.log', directory => 'DATA_PUMP_DIR', filetype => dbms_datapump.ku$_file_type_log_file);
DBMS_DATAPUMP.METADATA_FILTER(hdnl,'SCHEMA_EXPR','IN (''SCHEMA_1'')');
DBMS_DATAPUMP.START_JOB(hdnl);
END;
/
```

**Note**  
Data Pump jobs are started asynchronously\. For information about monitoring a Data Pump job, see [ Monitoring Job Status](https://docs.oracle.com/en/database/oracle/oracle-database/19/sutil/oracle-data-pump-overview.html#GUID-E365D74E-12CD-495C-BA23-5A55F679C7E7) in the Oracle documentation\. You can view the contents of the export log by using the `rdsadmin.rds_file_util.read_text_file` procedure\. For more information, see [Reading Files in a DB Instance Directory](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.ReadingFiles)\.

#### Step 3: Upload the Dump File to Your Amazon S3 Bucket<a name="Oracle.Procedural.Importing.DataPumpS3.Step3"></a>

Upload the dump file to the Amazon S3 bucket\. 

Use the Amazon RDS procedure `rdsadmin.rdsadmin_s3_tasks.upload_to_s3` to copy the dump file to the Amazon S3 bucket\. The following example uploads all of the files from the `DATA_PUMP_DIR` directory to an Amazon S3 bucket named `mys3bucket`\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.upload_to_s3(
      p_bucket_name    =>  'mys3bucket',       
      p_directory_name =>  'DATA_PUMP_DIR') 
   AS TASK_ID FROM DUAL;
```

The `SELECT` statement returns the ID of the task in a `VARCHAR2` data type\.

For more information, see [Uploading Files from an Oracle DB Instance to an Amazon S3 Bucket](oracle-s3-integration.md#oracle-s3-integration.using.upload)\.

#### Step 4: Copy the Exported Dump File from the Amazon S3 Bucket to the Target DB Instance<a name="Oracle.Procedural.Importing.DataPumpS3.Step4"></a>

Use SQL Plus or Oracle SQL Developer to connect to the Amazon RDS target Oracle DB instance\. Next, use the Amazon RDS procedure `rdsadmin.rdsadmin_s3_tasks.download_from_s3` to copy the dump file from the Amazon S3 bucket to the target DB instance\. The following example downloads all of the files from an Amazon S3 bucket named `mys3bucket` to the `DATA_PUMP_DIR` directory\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.download_from_s3(
      p_bucket_name    =>  'mys3bucket',       
      p_directory_name =>  'DATA_PUMP_DIR') 
   AS TASK_ID FROM DUAL;
```

The `SELECT` statement returns the ID of the task in a `VARCHAR2` data type\.

For more information, see [Downloading Files from an Amazon S3 Bucket to an Oracle DB Instance](oracle-s3-integration.md#oracle-s3-integration.using.download)\.

#### Step 5: Use DBMS\_DATAPUMP to Import the Data File on the Target DB Instance<a name="Oracle.Procedural.Importing.DataPumpS3.Step5"></a>

Use Oracle Data Pump to import the schema in the DB instance\. Additional options such as METADATA\_REMAP might be required\. 

 Connect to the DB instance with the Amazon RDS master user account to perform the import\. 

```
DECLARE
hdnl NUMBER;
BEGIN
hdnl := DBMS_DATAPUMP.OPEN( operation => 'IMPORT', job_mode => 'SCHEMA', job_name=>null);
DBMS_DATAPUMP.ADD_FILE( handle => hdnl, filename => 'sample_copied.dmp', directory => 'DATA_PUMP_DIR', filetype => dbms_datapump.ku$_file_type_dump_file);
DBMS_DATAPUMP.ADD_FILE( handle => hdnl, filename => 'sample_imp.log', directory => 'DATA_PUMP_DIR', filetype => dbms_datapump.ku$_file_type_log_file);
DBMS_DATAPUMP.METADATA_FILTER(hdnl,'SCHEMA_EXPR','IN (''SCHEMA_1'')');
DBMS_DATAPUMP.START_JOB(hdnl);
END;
/
```

**Note**  
Data Pump jobs are started asynchronously\. For information about monitoring a Data Pump job, see [ Monitoring Job Status](https://docs.oracle.com/en/database/oracle/oracle-database/19/sutil/oracle-data-pump-overview.html#GUID-E365D74E-12CD-495C-BA23-5A55F679C7E7) in the Oracle documentation\. You can view the contents of the import log by using the `rdsadmin.rds_file_util.read_text_file` procedure\. For more information, see [Reading Files in a DB Instance Directory](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.ReadingFiles)\.

You can verify the data import by viewing the user's tables on the DB instance\. For example, the following query returns the number of tables for `SCHEMA_1`\. 

```
select count(*) from dba_tables where owner='SCHEMA_1'; 
```

#### Step 6: Clean Up<a name="Oracle.Procedural.Importing.DataPumpS3.Step6"></a>

After the data has been imported, you can delete the files that you don't want to keep\. You can list the files in the DATA\_PUMP\_DIR using the following command\.

```
select * from table(RDSADMIN.RDS_FILE_UTIL.LISTDIR('DATA_PUMP_DIR')) order by mtime;
```

To delete files in the DATA\_PUMP\_DIR that you no longer require, use the following command\. 

```
exec utl_file.fremove('DATA_PUMP_DIR','<file name>');
```

For example, the following command deletes the file named `"sample_copied.dmp"`\. 

```
exec utl_file.fremove('DATA_PUMP_DIR','sample_copied.dmp'); 
```

### Importing Data with Oracle Data Pump and a Database Link<a name="Oracle.Procedural.Importing.DataPump.DBLink"></a>

 The following import process uses Oracle Data Pump and the Oracle [DBMS\_FILE\_TRANSFER](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_FILE_TRANSFER.html) package\. The process connects to a source Oracle instance, which can be an on\-premises or Amazon EC2 instance, or an Amazon RDS for Oracle DB instance\. The process then exports data using the [DBMS\_DATAPUMP](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_DATAPUMP.html) package\. Next, it uses the DBMS\_FILE\_TRANSFER\.PUT\_FILE method to copy the dump file from the Oracle instance to the DATA\_PUMP\_DIR directory on the target Amazon RDS Oracle DB instance that is connected using a database link\. The final step imports the data from the copied dump file into the Amazon RDS Oracle DB instance using the DBMS\_DATAPUMP package\. 

The process has the following requirements:
+ You must have execute privileges on the DBMS\_FILE\_TRANSFER and DBMS\_DATAPUMP packages\.
+ You must have write privileges to the DATA\_PUMP\_DIR directory on the source DB instance\.
+ You must ensure that you have enough storage space to store the dump file on the source instance and the target DB instance\.

**Note**  
This process imports a dump file into the DATA\_PUMP\_DIR directory, a preconfigured directory on all Oracle DB instances\. This directory is located on the same storage volume as your data files\. When you import the dump file, the existing Oracle data files use more space\. Thus, you should make sure that your DB instance can accommodate that additional use of space\. The imported dump file is not automatically deleted or purged from the DATA\_PUMP\_DIR directory\. To remove the imported dump file, use [UTL\_FILE\.FREMOVE](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/UTL_FILE.html#GUID-09B09C2A-2C21-4F70-BF04-D0EEA7B59CAF), found on the Oracle website\. 

The import process using Oracle Data Pump and the DBMS\_FILE\_TRANSFER package has the following steps\.

**Topics**
+ [Step 1: Grant Privileges to the User on the Amazon RDS Target Instance](#Oracle.Procedural.Importing.DataPumpDBLink.Step1)
+ [Step 2: Grant Privileges to the User on the Source Database](#Oracle.Procedural.Importing.DataPumpDBLink.Step2)
+ [Step 3: Use DBMS\_DATAPUMP to Create a Dump File](#Oracle.Procedural.Importing.DataPumpDBLink.Step3)
+ [Step 4: Create a Database Link to the Target DB Instance](#Oracle.Procedural.Importing.DataPumpDBLink.Step4)
+ [Step 5: Use DBMS\_FILE\_TRANSFER to Copy the Exported Dump File to the Target DB Instance](#Oracle.Procedural.Importing.DataPumpDBLink.Step5)
+ [Step 6: Use DBMS\_DATAPUMP to Import the Data File to the Target DB Instance](#Oracle.Procedural.Importing.DataPumpDBLink.Step6)
+ [Step 7: Clean Up](#Oracle.Procedural.Importing.DataPumpDBLink.Step7)

#### Step 1: Grant Privileges to the User on the Amazon RDS Target Instance<a name="Oracle.Procedural.Importing.DataPumpDBLink.Step1"></a>

To grant privileges to the user on the RDS target instance, take the following steps:

1. Use SQL Plus or Oracle SQL Developer to connect to the Amazon RDS target Oracle DB instance into which the data will be imported\. Connect as the Amazon RDS master user\. For information about connecting to the DB instance, see [Connecting to a DB Instance Running the Oracle Database Engine](USER_ConnectToOracleInstance.md)\.

1. Create the required tablespaces before you import the data\. For more information, see [Creating and Sizing Tablespaces](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.CreatingTablespacesAndDatafiles)\.

1. If the user account into which the data is imported doesn't exist, create the user account and grant the necessary permissions and roles\. If you plan to import data into multiple user schemas, create each user account and grant the necessary privileges and roles to it\.

   For example, the following commands create a new user and grant the necessary permissions and roles to import the data into the user's schema\.

   ```
   create user schema_1 identified by <password>;
   grant create session, resource to schema_1;
   alter user schema_1 quota 100M on users;
   ```

   This example grants the new user the `CREATE SESSION` privilege and the `RESOURCE` role\. Additional privileges and roles might be required depending on the database objects that you import\. 
**Note**  
Replace `schema_1` with the name of your schema in this step and in the following steps\.

#### Step 2: Grant Privileges to the User on the Source Database<a name="Oracle.Procedural.Importing.DataPumpDBLink.Step2"></a>

Use SQL Plus or Oracle SQL Developer to connect to the Oracle instance that contains the data to be imported\. If necessary, create a user account and grant the necessary permissions\. 

**Note**  
If the source database is an Amazon RDS instance, you can skip this step\. You use your Amazon RDS master user account to perform the export\.

The following commands create a new user and grant the necessary permissions\.

```
create user export_user identified by <password>;
grant create session, create table, create database link to export_user;
alter user export_user quota 100M on users;
grant read, write on directory data_pump_dir to export_user;
grant select_catalog_role to export_user;
grant execute on dbms_datapump to export_user;
grant execute on dbms_file_transfer to export_user;
```

#### Step 3: Use DBMS\_DATAPUMP to Create a Dump File<a name="Oracle.Procedural.Importing.DataPumpDBLink.Step3"></a>

 Use SQL Plus or Oracle SQL Developer to connect to the source Oracle instance with an administrative user or with the user you created in step 2\. If the source database is an Amazon RDS Oracle DB instance, connect with the Amazon RDS master user\. Next, use the Oracle Data Pump utility to create a dump file\. 

The following script creates a dump file named *sample\.dmp* in the DATA\_PUMP\_DIR directory\. 

```
DECLARE
hdnl NUMBER;
BEGIN
hdnl := DBMS_DATAPUMP.OPEN( operation => 'EXPORT', job_mode => 'SCHEMA', job_name=>null);
DBMS_DATAPUMP.ADD_FILE( handle => hdnl, filename => 'sample.dmp', directory => 'DATA_PUMP_DIR', filetype => dbms_datapump.ku$_file_type_dump_file);
DBMS_DATAPUMP.ADD_FILE( handle => hdnl, filename => 'sample_exp.log', directory => 'DATA_PUMP_DIR', filetype => dbms_datapump.ku$_file_type_log_file);
DBMS_DATAPUMP.METADATA_FILTER(hdnl,'SCHEMA_EXPR','IN (''SCHEMA_1'')');
DBMS_DATAPUMP.START_JOB(hdnl);
END;
/
```

**Note**  
Data Pump jobs are started asynchronously\. For information about monitoring a Data Pump job, see [ Monitoring Job Status](https://docs.oracle.com/en/database/oracle/oracle-database/19/sutil/oracle-data-pump-overview.html#GUID-E365D74E-12CD-495C-BA23-5A55F679C7E7) in the Oracle documentation\. You can view the contents of the export log by using the `rdsadmin.rds_file_util.read_text_file` procedure\. For more information, see [Reading Files in a DB Instance Directory](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.ReadingFiles)\.

#### Step 4: Create a Database Link to the Target DB Instance<a name="Oracle.Procedural.Importing.DataPumpDBLink.Step4"></a>

Create a database link between your source instance and your target DB instance\. Your local Oracle instance must have network connectivity to the DB instance in order to create a database link and to transfer your export dump file\. 

Perform this step connected with the same user account as the previous step\.

If you are creating a database link between two DB instances inside the same VPC or peered VPCs, the two DB instances should have a valid route between them\. The security group of each DB instance must allow ingress to and egress from the other DB instance\. The security group inbound and outbound rules can refer to security groups from the same VPC or a peered VPC\. For more information, see [Adjusting Database Links for Use with DB Instances in a VPC](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.DBLinks)\. 

The following command creates a database link named `to_rds` that connects to the Amazon RDS master user at the target DB instance\. 

```
create database link to_rds connect to <master_user_account> identified by <password>
using '(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<dns or ip address of remote db>)(PORT=<listener port>))(CONNECT_DATA=(SID=<remote SID>)))';
```

#### Step 5: Use DBMS\_FILE\_TRANSFER to Copy the Exported Dump File to the Target DB Instance<a name="Oracle.Procedural.Importing.DataPumpDBLink.Step5"></a>

Use DBMS\_FILE\_TRANSFER to copy the dump file from the source database instance to the target DB instance\. The following script copies a dump file named sample\.dmp from the source instance to a target database link named *to\_rds* \(created in the previous step\)\. 

```
BEGIN
DBMS_FILE_TRANSFER.PUT_FILE(
source_directory_object       => 'DATA_PUMP_DIR',
source_file_name              => 'sample.dmp',
destination_directory_object  => 'DATA_PUMP_DIR',
destination_file_name         => 'sample_copied.dmp', 
destination_database          => 'to_rds' 
);
END;
/
```

#### Step 6: Use DBMS\_DATAPUMP to Import the Data File to the Target DB Instance<a name="Oracle.Procedural.Importing.DataPumpDBLink.Step6"></a>

Use Oracle Data Pump to import the schema in the DB instance\. Additional options such as METADATA\_REMAP might be required\. 

 Connect to the DB instance with the Amazon RDS master user account to perform the import\. 

```
DECLARE
hdnl NUMBER;
BEGIN
hdnl := DBMS_DATAPUMP.OPEN( operation => 'IMPORT', job_mode => 'SCHEMA', job_name=>null);
DBMS_DATAPUMP.ADD_FILE( handle => hdnl, filename => 'sample_copied.dmp', directory => 'DATA_PUMP_DIR', filetype => dbms_datapump.ku$_file_type_dump_file);
DBMS_DATAPUMP.ADD_FILE( handle => hdnl, filename => 'sample_imp.log', directory => 'DATA_PUMP_DIR', filetype => dbms_datapump.ku$_file_type_log_file);
DBMS_DATAPUMP.METADATA_FILTER(hdnl,'SCHEMA_EXPR','IN (''SCHEMA_1'')');
DBMS_DATAPUMP.START_JOB(hdnl);
END;
/
```

**Note**  
Data Pump jobs are started asynchronously\. For information about monitoring a Data Pump job, see [ Monitoring Job Status](https://docs.oracle.com/en/database/oracle/oracle-database/19/sutil/oracle-data-pump-overview.html#GUID-E365D74E-12CD-495C-BA23-5A55F679C7E7) in the Oracle documentation\. You can view the contents of the import log by using the `rdsadmin.rds_file_util.read_text_file` procedure\. For more information, see [Reading Files in a DB Instance Directory](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.ReadingFiles)\.

You can verify the data import by viewing the user's tables on the DB instance\. For example, the following query returns the number of tables for `schema_1`\. 

```
select count(*) from dba_tables where owner='SCHEMA_1'; 
```

#### Step 7: Clean Up<a name="Oracle.Procedural.Importing.DataPumpDBLink.Step7"></a>

After the data has been imported, you can delete the files that you don't want to keep\. You can list the files in the DATA\_PUMP\_DIR using the following command\.

```
select * from table(RDSADMIN.RDS_FILE_UTIL.LISTDIR('DATA_PUMP_DIR')) order by mtime;
```

To delete files in the DATA\_PUMP\_DIR that you no longer require, use the following command\. 

```
exec utl_file.fremove('DATA_PUMP_DIR','<file name>');
```

For example, the following command deletes the file named `"sample_copied.dmp"`\. 

```
exec utl_file.fremove('DATA_PUMP_DIR','sample_copied.dmp'); 
```

## Oracle Export/Import Utilities<a name="Oracle.Procedural.Importing.ExportImport"></a>

The Oracle Export/Import utilities are best suited for migrations where the data size is small and data types such as binary float and double are not required\. The import process creates the schema objects so you do not need to run a script to create them beforehand, making this process well suited for databases with small tables\. The following example demonstrates how these utilities can be used to export and import specific tables\. 

To download Oracle export and import utilities, go to [http://www\.oracle\.com/technetwork/database/enterprise\-edition/downloads/index\.html](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)\. 

Export the tables from the source database using the command below\. Substitute username/password as appropriate\. 

```
exp cust_dba@ORCL FILE=exp_file.dmp TABLES=(tab1,tab2,tab3) LOG=exp_file.log
```

The export process creates a binary dump file that contains both the schema and data for the specified tables\. Now this schema and data can be imported into a target database using the command: 

```
imp cust_dba@targetdb FROMUSER=cust_schema TOUSER=cust_schema \  
TABLES=(tab1,tab2,tab3) FILE=exp_file.dmp LOG=imp_file.log
```

There are other variations of the Export and Import commands that might be better suited to your needs\. See Oracle's documentation for full details\. 

## Oracle SQL\*Loader<a name="Oracle.Procedural.Importing.SQLLoader"></a>

Oracle SQL\*Loader is well suited for large databases that have a limited number of objects in them\. Since the process involved in exporting from a source database and loading to a target database is very specific to the schema, the following example creates the sample schema objects, exports from a source, and then loads it into a target database\. 

To download Oracle SQL\*Loader, go to [http://www\.oracle\.com/technetwork/database/enterprise\-edition/downloads/index\.html](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)\. 

1. Create a sample source table using the command below\.

   ```
   create table customer_0 tablespace users as select rownum id, o.* from 
   all_objects o, all_objects x where rownum <= 1000000;
   ```

1. On the target Amazon RDS instance, create a destination table that is used to load the data\. 

   ```
   create table customer_1 tablespace users as select 0 as id, owner, 
   object_name, created from all_objects where 1=2;
   ```

1. The data is exported from the source database to a flat file with delimiters\. This example uses SQL\*Plus for this purpose\. For your data, you will likely need to generate a script that does the export for all the objects in the database\. 

   ```
   alter session set nls_date_format = 'YYYY/MM/DD HH24:MI:SS'; set linesize 800 
   HEADING OFF FEEDBACK OFF array 5000 pagesize 0 spool customer_0.out SET 
   MARKUP HTML PREFORMAT ON SET COLSEP ',' SELECT id, owner, object_name, 
   created FROM customer_0; spool off
   ```

1. You need to create a control file to describe the data\. Again, depending on your data, you will need to build a script that does this step\. 

   ```
   cat << EOF > sqlldr_1.ctl 
   load data
   infile customer_0.out
   into table customer_1
   APPEND
   fields terminated by "," optionally enclosed by '"'
   (
   id     	    	  POSITION(01:10)         INTEGER EXTERNAL,
   owner         	  POSITION(12:41)         CHAR,
   object_name   	  POSITION(43:72)         CHAR,
   created	          POSITION(74:92)         date "YYYY/MM/DD HH24:MI:SS"
   )
   ```

   If needed, copy the files generated by the preceding code to a staging area, such as an Amazon EC2 instance\. 

1. Finally, import the data using SQL\*Loader with the appropriate username and password for the target database\. 

   ```
   sqlldr cust_dba@targetdb control=sqlldr_1.ctl BINDSIZE=10485760 READSIZE=10485760 ROWS=1000 
   ```

## Oracle Materialized Views<a name="Oracle.Procedural.Importing.Materialized"></a>

You can also make use of Oracle materialized view replication to migrate large datasets efficiently\. Replication allows you to keep the target tables in sync with the source on an ongoing basis, so the actual cutover to Amazon RDS can be done later, if needed\. The replication is set up using a database link from the Amazon RDS instance to the source database\. 

One requirement for materialized views is to allow access from the target database to the source database\. In the following example, access rules were enabled on the source database to allow the Amazon RDS target database to connect to the source over SQLNet\. 

1. Create a user account on both source and Amazon RDS target instances that can authenticate with the same password\. 

   ```
   create user dblink_user identified by <password>       
      default tablespace users       
      temporary tablespace temp; 
      
   grant create session to dblink_user; 
   
   grant select any table to dblink_user; 
   
   grant select any dictionary to dblink_user;
   ```

1. Create a database link from the Amazon RDS target instance to the source instance using the newly created dblink\_user\. 

   ```
   create database link remote_site
      connect to dblink_user identified by <password>
      using '(description=(address=(protocol=tcp) (host=<myhost>) 
      (port=<listener port>)) (connect_data=(sid=<sourcedb sid>)))';
   ```

1. Test the link:

   ```
   select * from v$instance@remote_site; 
   ```

1. Create a sample table with primary key and materialized view log on the source instance\. 

   ```
   create table customer_0 tablespace users as select rownum id, o.* from 
      all_objects o, all_objects x where rownum <= 1000000; 
      
   alter table customer_0 add constraint pk_customer_0 primary key (id) using index;
      
   create materialized view log on customer_0;
   ```

1. On the target Amazon RDS instance, create a materialized view\. 

   ```
   CREATE MATERIALIZED VIEW customer_0 BUILD IMMEDIATE REFRESH FAST AS 
      SELECT * FROM cust_dba.customer_0@remote_site;
   ```

1. On the target Amazon RDS instance, refresh the materialized view\.

   ```
   exec DBMS_MV.REFRESH('CUSTOMER_0', 'f');
   ```

1. Drop the materialized view and include the PRESERVE TABLE clause to retain the materialized view container table and its contents\.

   ```
   DROP MATERIALIZED VIEW customer_0 PRESERVE TABLE;
   ```

   The retained table has the same name as the dropped materialized view\.