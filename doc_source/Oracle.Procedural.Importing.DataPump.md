# Importing using Oracle Data Pump<a name="Oracle.Procedural.Importing.DataPump"></a>

Oracle Data Pump is a utility that allows you to export Oracle data to a dump file and import it into another Oracle database\. It is a long\-term replacement for the Oracle Export/Import utilities\. Oracle Data Pump is the recommended way to move large amounts of data from an Oracle database to an Amazon RDS DB instance\.

The examples in this section show one way to import data into an Oracle database, but Oracle Data Pump supports other techniques\. For more information, see the [Oracle Database documentation](https://docs.oracle.com/en/database/oracle/oracle-database/19/sutil/oracle-data-pump.html#GUID-501A9908-BCC5-434C-8853-9A6096766B5A)\.

The examples in this section use the `DBMS_DATAPUMP` package\. You can accomplish the same tasks using the Oracle Data Pump command line utilities `impdp` and `expdp`\. You can install these utilities on a remote host as part of an Oracle Client installation, including Oracle Instant Client\. For more information, see [How do I use Oracle Instant Client to run Data Pump Import or Export for my Amazon RDS for Oracle DB instance?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-oracle-instant-client-datapump/)

**Topics**
+ [Overview of Oracle Data Pump](#Oracle.Procedural.Importing.DataPump.Overview)
+ [Importing data with Oracle Data Pump and an Amazon S3 bucket](#Oracle.Procedural.Importing.DataPump.S3)
+ [Importing data with Oracle Data Pump and a database link](#Oracle.Procedural.Importing.DataPump.DBLink)

## Overview of Oracle Data Pump<a name="Oracle.Procedural.Importing.DataPump.Overview"></a>

Oracle Data Pump is made up of the following components:
+ Command\-line clients `expdp` and `impdp`
+ The `DBMS_DATAPUMP` PL/SQL package
+ The `DBMS_METADATA` PL/SQL package

You can use Oracle Data Pump for the following scenarios:
+ Import data from an Oracle database, either on\-premises or on an Amazon EC2 instance, to an RDS for Oracle DB instance\.
+ Import data from an RDS for Oracle DB instance to an Oracle database, either on\-premises or on an Amazon EC2 instance\.
+ Import data between RDS for Oracle DB instances, for example, to migrate data from EC2\-Classic to VPC\.

To download Oracle Data Pump utilities, see [Oracle database software downloads](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html) on the Oracle Technology Network website\. For compatibility considerations when migrating between versions of Oracle Database, see the [ Oracle Database documentation](https://docs.oracle.com/en/database/oracle/oracle-database/19/sutil/oracle-data-pump-overview.html#GUID-BAA3B679-A758-4D55-9820-432D9EB83C68)\.

### Oracle Data Pump workflow<a name="Oracle.Procedural.Importing.DataPump.Overview.how-it-works"></a>

Typically, you use Oracle Data Pump in the following stages:

1. Export your data into a dump file on the source database\.

1. Upload your dump file to your destination RDS for Oracle DB instance\. You can transfer using an Amazon S3 bucket or by using a database link between the two databases\.

1. Import the data from your dump file into your RDS for Oracle DB instance\.

### Oracle Data Pump best practices<a name="Oracle.Procedural.Importing.DataPump.Overview.best-practices"></a>

When you use Oracle Data Pump to import data into an RDS for Oracle instance, we recommend the following best practices:
+ Perform imports in `schema` or `table` mode to import specific schemas and objects\.
+ Limit the schemas you import to those required by your application\.
+ Don't import in `full` mode or import schemas for system\-maintained components\.

  Because RDS for Oracle doesn't allow access to `SYS` or `SYSDBA` administrative users, these actions might damage the Oracle data dictionary and affect the stability of your database\.
+ When loading large amounts of data, do the following:

  1. Transfer the dump file to the target RDS for Oracle DB instance\.

  1. Take a DB snapshot of your instance\.

  1. Test the import to verify that it succeeds\.

  If database components are invalidated, you can delete the DB instance and re\-create it from the DB snapshot\. The restored DB instance includes any dump files staged on the DB instance when you took the DB snapshot\.
+ Don't import dump files that were created using the Oracle Data Pump export parameters `TRANSPORT_TABLESPACES`, `TRANSPORTABLE`, or `TRANSPORT_FULL_CHECK`\. RDS for Oracle DB instances don't support importing these dump files\.
+ Don't import dump files that contain Oracle Scheduler objects in `SYS`, `SYSTEM`, `RDSADMIN`, `RDSSEC`, and `RDS_DATAGUARD`, and belong to the following categories:
  + Jobs
  + Programs
  + Schedules
  + Chains
  + Rules
  + Evaluation contexts
  + Rule sets

  RDS for Oracle DB instances don't support importing these dump files\. 
+ To exclude unsupported Oracle Scheduler objects, use additional directives during the Data Pump export\. If you use `DBMS_DATAPUMP`, you can add an additional `METADATA_FILTER` before the `DBMS_METADATA.START_JOB`:

  ```
  DBMS_DATAPUMP.METADATA_FILTER(
    v_hdnl,
    'EXCLUDE_NAME_EXPR',
    q'[IN (SELECT NAME FROM SYS.OBJ$ 
           WHERE TYPE# IN (66,67,74,79,59,62,46) 
           AND OWNER# IN
             (SELECT USER# FROM SYS.USER$ 
              WHERE NAME IN ('RDSADMIN','SYS','SYSTEM','RDS_DATAGUARD','RDSSEC')
              )
          )
    ]',
    'PROCOBJ'
  );
  ```

  If you use `expdp`, create a parameter file that contains the `exclude` directive shown in the following example\. Then use `PARFILE=parameter_file` with your `expdp` command\.

  ```
  exclude=procobj:"IN 
    (SELECT NAME FROM sys.OBJ$
     WHERE TYPE# IN (66,67,74,79,59,62,46) 
     AND OWNER# IN 
       (SELECT USER# FROM SYS.USER$ 
        WHERE NAME IN ('RDSADMIN','SYS','SYSTEM','RDS_DATAGUARD','RDSSEC')
       )
    )"
  ```

## Importing data with Oracle Data Pump and an Amazon S3 bucket<a name="Oracle.Procedural.Importing.DataPump.S3"></a>

The following import process uses Oracle Data Pump and an Amazon S3 bucket\. The steps are as follows:

1. Export data on the source database using the Oracle [DBMS\_DATAPUMP](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_DATAPUMP.html) package\.

1. Place the dump file in an Amazon S3 bucket\.

1. Download the dump file from the Amazon S3 bucket to the `DATA_PUMP_DIR` directory on the target RDS for Oracle DB instance\. 

1. Import the data from the copied dump file into the RDS for Oracle DB instance using the package `DBMS_DATAPUMP`\.

**Topics**
+ [Requirements for Importing data with Oracle Data Pump and an Amazon S3 bucket](#Oracle.Procedural.Importing.DataPumpS3.requirements)
+ [Step 1: Grant privileges to the database user on the RDS for Oracle target DB instance](#Oracle.Procedural.Importing.DataPumpS3.Step1)
+ [Step 2: Export data into a dump file using DBMS\_DATAPUMP](#Oracle.Procedural.Importing.DataPumpS3.Step2)
+ [Step 3: Upload the dump file to your Amazon S3 bucket](#Oracle.Procedural.Importing.DataPumpS3.Step3)
+ [Step 4: Download the dump file from your Amazon S3 bucket to your target DB instance](#Oracle.Procedural.Importing.DataPumpS3.Step4)
+ [Step 5: Import your dump file into your target DB instance using DBMS\_DATAPUMP](#Oracle.Procedural.Importing.DataPumpS3.Step5)
+ [Step 6: Clean up](#Oracle.Procedural.Importing.DataPumpS3.Step6)

### Requirements for Importing data with Oracle Data Pump and an Amazon S3 bucket<a name="Oracle.Procedural.Importing.DataPumpS3.requirements"></a>

The process has the following requirements:
+ Make sure that an Amazon S3 bucket is available for file transfers, and that the Amazon S3 bucket is in the same AWS Region as the DB instance\. For instructions, see [Create a bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\.
+ The object that you upload into the Amazon S3 bucket must be 5 TB or less\. For more information about working with objects in Amazon S3, see [Amazon Simple Storage Service User Guide](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingObjects.html)\.
**Note**  
If you dump file exceeds 5 TB, you can run the Oracle Data Pump export with the parallel option\. This operation spreads the data into multiple dump files so that you do not exceed the 5 TB limit for individual files\.
+ You must prepare the Amazon S3 bucket for Amazon RDS integration by following the instructions in [Configuring IAM permissions for RDS for Oracle integration with Amazon S3](oracle-s3-integration.md#oracle-s3-integration.preparing)\.
+ You must ensure that you have enough storage space to store the dump file on the source instance and the target DB instance\.

**Note**  
This process imports a dump file into the `DATA_PUMP_DIR` directory, a preconfigured directory on all Oracle DB instances\. This directory is located on the same storage volume as your data files\. When you import the dump file, the existing Oracle data files use more space\. Thus, you should make sure that your DB instance can accommodate that additional use of space\. The imported dump file is not automatically deleted or purged from the `DATA_PUMP_DIR` directory\. To remove the imported dump file, use [UTL\_FILE\.FREMOVE](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/UTL_FILE.html#GUID-09B09C2A-2C21-4F70-BF04-D0EEA7B59CAF), found on the Oracle website\. 

### Step 1: Grant privileges to the database user on the RDS for Oracle target DB instance<a name="Oracle.Procedural.Importing.DataPumpS3.Step1"></a>

In this step, you create the schemas into which you plan to import data and grant the users necessary privileges\.

**To create users and grant necessary privileges on the RDS for Oracle target instance**

1. Use SQL\*Plus or Oracle SQL Developer to log in as the master user to the RDS for Oracle DB instance into which the data will be imported\. For information about connecting to a DB instance, see [Connecting to your Oracle DB instance](USER_ConnectToOracleInstance.md)\.

1. Create the required tablespaces before you import the data\. For more information, see [Creating and sizing tablespaces](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.CreatingTablespacesAndDatafiles)\.

1. Create the user account and grant the necessary permissions and roles if the user account into which the data is imported doesn't exist\. If you plan to import data into multiple user schemas, create each user account and grant the necessary privileges and roles to it\.

   For example, the following SQL statements create a new user and grant the necessary permissions and roles to import the data into the schema owned by this user\. Replace `schema_1` with the name of your schema in this step and in the following steps\.

   ```
   CREATE USER schema_1 IDENTIFIED BY my_password;
   GRANT CREATE SESSION, RESOURCE TO schema_1;
   ALTER USER schema_1 QUOTA 100M ON users;
   ```

   The preceding statements grant the new user the `CREATE SESSION` privilege and the `RESOURCE` role\. You might need additional privileges and roles depending on the database objects that you import\.

### Step 2: Export data into a dump file using DBMS\_DATAPUMP<a name="Oracle.Procedural.Importing.DataPumpS3.Step2"></a>

To create a dump file, use the `DBMS_DATAPUMP` package\.

**To export Oracle data into a dump file**

1. Use SQL Plus or Oracle SQL Developer to connect to the source RDS for Oracle DB instance with an administrative user\. If the source database is an RDS for Oracle DB instance, connect with the Amazon RDS master user\.

1. Export the data by calling `DBMS_DATAPUMP` procedures\.

   The following script exports the `SCHEMA_1` schema into a dump file named `sample.dmp` in the `DATA_PUMP_DIR` directory\. Replace `SCHEMA_1` with the name of the schema that you want to export\.

   ```
   DECLARE
     v_hdnl NUMBER;
   BEGIN
     v_hdnl := DBMS_DATAPUMP.OPEN(
       operation => 'EXPORT', 
       job_mode  => 'SCHEMA', 
       job_name  => null
     );
     DBMS_DATAPUMP.ADD_FILE( 
       handle    => v_hdnl         , 
       filename  => 'sample.dmp'   , 
       directory => 'DATA_PUMP_DIR', 
       filetype  => dbms_datapump.ku$_file_type_dump_file
     );
     DBMS_DATAPUMP.ADD_FILE( 
       handle    => v_hdnl, 
       filename  => 'sample_exp.log', 
       directory => 'DATA_PUMP_DIR' , 
       filetype  => dbms_datapump.ku$_file_type_log_file
     );
     DBMS_DATAPUMP.METADATA_FILTER(v_hdnl,'SCHEMA_EXPR','IN (''SCHEMA_1'')');
     DBMS_DATAPUMP.METADATA_FILTER(
       v_hdnl,
       'EXCLUDE_NAME_EXPR',
       q'[IN (SELECT NAME FROM SYS.OBJ$ 
              WHERE TYPE# IN (66,67,74,79,59,62,46) 
              AND OWNER# IN 
                (SELECT USER# FROM SYS.USER$ 
                 WHERE NAME IN ('RDSADMIN','SYS','SYSTEM','RDS_DATAGUARD','RDSSEC')
                )
             )
       ]',
       'PROCOBJ'
     );
     DBMS_DATAPUMP.START_JOB(v_hdnl);
   END;
   /
   ```
**Note**  
Data Pump starts jobs asynchronously\. For information about monitoring a Data Pump job, see [ Monitoring job status](https://docs.oracle.com/en/database/oracle/oracle-database/19/sutil/oracle-data-pump-overview.html#GUID-E365D74E-12CD-495C-BA23-5A55F679C7E7) in the Oracle documentation\. 

1. \(Optional\) View the contents of the export log by calling the `rdsadmin.rds_file_util.read_text_file` procedure\. For more information, see [Reading files in a DB instance directory](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.ReadingFiles)\.

### Step 3: Upload the dump file to your Amazon S3 bucket<a name="Oracle.Procedural.Importing.DataPumpS3.Step3"></a>

Use the Amazon RDS procedure `rdsadmin.rdsadmin_s3_tasks.upload_to_s3` to copy the dump file to the Amazon S3 bucket\. The following example uploads all of the files from the `DATA_PUMP_DIR` directory to an Amazon S3 bucket named `myS3bucket`\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.upload_to_s3(
  p_bucket_name    =>  'myS3bucket',       
  p_directory_name =>  'DATA_PUMP_DIR') 
AS TASK_ID FROM DUAL;
```

The `SELECT` statement returns the ID of the task in a `VARCHAR2` data type\. For more information, see [Uploading files from your RDS for Oracle DB instance to an Amazon S3 bucket](oracle-s3-integration.md#oracle-s3-integration.using.upload)\.

### Step 4: Download the dump file from your Amazon S3 bucket to your target DB instance<a name="Oracle.Procedural.Importing.DataPumpS3.Step4"></a>

Perform this step using the Amazon RDS procedure `rdsadmin.rdsadmin_s3_tasks.download_from_s3`\. When you download a file to a directory, the procedure `download_from_s3` skips the download if an identically named file already exists in the directory\. To remove a file from the download directory, use [UTL\_FILE\.FREMOVE](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/UTL_FILE.html#GUID-09B09C2A-2C21-4F70-BF04-D0EEA7B59CAF), found on the Oracle website\.

**To download your dump file**

1. Start SQL\*Plus or Oracle SQL Developer and log in as the master on your Amazon RDS target Oracle DB instance

1. Download the dump file using the Amazon RDS procedure `rdsadmin.rdsadmin_s3_tasks.download_from_s3`\.

   The following example downloads all files from an Amazon S3 bucket named `myS3bucket` to the directory `DATA_PUMP_DIR`\.

   ```
   SELECT rdsadmin.rdsadmin_s3_tasks.download_from_s3(
     p_bucket_name    =>  'myS3bucket',
     p_directory_name =>  'DATA_PUMP_DIR')
   AS TASK_ID FROM DUAL;
   ```

   The `SELECT` statement returns the ID of the task in a `VARCHAR2` data type\. For more information, see [Downloading files from an Amazon S3 bucket to an Oracle DB instance](oracle-s3-integration.md#oracle-s3-integration.using.download)\.

### Step 5: Import your dump file into your target DB instance using DBMS\_DATAPUMP<a name="Oracle.Procedural.Importing.DataPumpS3.Step5"></a>

Use `DBMS_DATAPUMP` to import the schema into your RDS for Oracle DB instance\. Additional options such as `METADATA_REMAP` might be required\.

**To import data into your target DB instance**

1. Start SQL\*Plus or SQL Developer and log in as the master user to your RDS for Oracle DB instance\.

1. Export the data by calling `DBMS_DATAPUMP` procedures\.

   The following example imports the *SCHEMA\_1* data from `sample_copied.dmp` into your target DB instance\.

   ```
   DECLARE
     v_hdnl NUMBER;
   BEGIN
     v_hdnl := DBMS_DATAPUMP.OPEN( 
       operation => 'IMPORT', 
       job_mode  => 'SCHEMA', 
       job_name  => null);
     DBMS_DATAPUMP.ADD_FILE( 
       handle    => v_hdnl, 
       filename  => 'sample_copied.dmp', 
       directory => 'DATA_PUMP_DIR', 
       filetype  => dbms_datapump.ku$_file_type_dump_file);
     DBMS_DATAPUMP.ADD_FILE( 
       handle    => v_hdnl, 
       filename  => 'sample_imp.log', 
       directory => 'DATA_PUMP_DIR', 
       filetype  => dbms_datapump.ku$_file_type_log_file);
     DBMS_DATAPUMP.METADATA_FILTER(v_hdnl,'SCHEMA_EXPR','IN (''SCHEMA_1'')');
     DBMS_DATAPUMP.START_JOB(v_hdnl);
   END;
   /
   ```
**Note**  
Data Pump jobs are started asynchronously\. For information about monitoring a Data Pump job, see [ Monitoring job status](https://docs.oracle.com/en/database/oracle/oracle-database/19/sutil/oracle-data-pump-overview.html#GUID-E365D74E-12CD-495C-BA23-5A55F679C7E7) in the Oracle documentation\. You can view the contents of the import log by using the `rdsadmin.rds_file_util.read_text_file` procedure\. For more information, see [Reading files in a DB instance directory](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.ReadingFiles)\.

1. Verify the data import by listing the schema tables on your target DB instance\.

   For example, the following query returns the number of tables for `SCHEMA_1`\. 

   ```
   SELECT COUNT(*) FROM DBA_TABLES WHERE OWNER='SCHEMA_1';
   ```

### Step 6: Clean up<a name="Oracle.Procedural.Importing.DataPumpS3.Step6"></a>

After the data has been imported, you can delete the files that you don't want to keep\.

**To remove unneeded files**

1. Start SQL\*Plus or SQL Developer and log in as the master user to your RDS for Oracle DB instance\.

1. List the files in `DATA_PUMP_DIR` using the following command\.

   ```
   SELECT * FROM TABLE(rdsadmin.rds_file_util.listdir('DATA_PUMP_DIR')) ORDER BY MTIME;
   ```

1. Delete files in `DATA_PUMP_DIR` that you no longer require, use the following command\.

   ```
   EXEC UTL_FILE.FREMOVE('DATA_PUMP_DIR','filename');
   ```

   For example, the following command deletes the file named `sample_copied.dmp`\.

   ```
   EXEC UTL_FILE.FREMOVE('DATA_PUMP_DIR','sample_copied.dmp'); 
   ```

## Importing data with Oracle Data Pump and a database link<a name="Oracle.Procedural.Importing.DataPump.DBLink"></a>

The following import process uses Oracle Data Pump and the Oracle [DBMS\_FILE\_TRANSFER](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_FILE_TRANSFER.html) package\. The steps are as follows:

1. Connect to a source Oracle database, which can be an on\-premises database, Amazon EC2 instance, or an RDS for Oracle DB instance\. 

1. Export data using the [DBMS\_DATAPUMP](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_DATAPUMP.html) package\.

1. Use `DBMS_FILE_TRANSFER.PUT_FILE` to copy the dump file from the Oracle database to the `DATA_PUMP_DIR` directory on the target RDS for Oracle DB instance that is connected using a database link\. 

1. Import the data from the copied dump file into the RDS for Oracle DB instance using the ` DBMS_DATAPUMP` package\.

The import process using Oracle Data Pump and the `DBMS_FILE_TRANSFER` package has the following steps\.

**Topics**
+ [Requirements for importing data with Oracle Data Pump and a database link](#Oracle.Procedural.Importing.DataPumpDBLink.requirements)
+ [Step 1: Grant privileges to the user on the RDS for Oracle target DB instance](#Oracle.Procedural.Importing.DataPumpDBLink.Step1)
+ [Step 2: Grant privileges to the user on the source database](#Oracle.Procedural.Importing.DataPumpDBLink.Step2)
+ [Step 3: Create a dump file using DBMS\_DATAPUMP](#Oracle.Procedural.Importing.DataPumpDBLink.Step3)
+ [Step 4: Create a database link to the target DB instance](#Oracle.Procedural.Importing.DataPumpDBLink.Step4)
+ [Step 5: Copy the exported dump file to the target DB instance using DBMS\_FILE\_TRANSFER](#Oracle.Procedural.Importing.DataPumpDBLink.Step5)
+ [Step 6: Import the data file to the target DB instance using DBMS\_DATAPUMP](#Oracle.Procedural.Importing.DataPumpDBLink.Step6)
+ [Step 7: Clean up](#Oracle.Procedural.Importing.DataPumpDBLink.Step7)

### Requirements for importing data with Oracle Data Pump and a database link<a name="Oracle.Procedural.Importing.DataPumpDBLink.requirements"></a>

The process has the following requirements:
+ You must have execute privileges on the `DBMS_FILE_TRANSFER` and `DBMS_DATAPUMP` packages\.
+ You must have write privileges to the `DATA_PUMP_DIR` directory on the source DB instance\.
+ You must ensure that you have enough storage space to store the dump file on the source instance and the target DB instance\.

**Note**  
This process imports a dump file into the `DATA_PUMP_DIR` directory, a preconfigured directory on all Oracle DB instances\. This directory is located on the same storage volume as your data files\. When you import the dump file, the existing Oracle data files use more space\. Thus, you should make sure that your DB instance can accommodate that additional use of space\. The imported dump file is not automatically deleted or purged from the `DATA_PUMP_DIR` directory\. To remove the imported dump file, use [UTL\_FILE\.FREMOVE](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/UTL_FILE.html#GUID-09B09C2A-2C21-4F70-BF04-D0EEA7B59CAF), found on the Oracle website\. 

### Step 1: Grant privileges to the user on the RDS for Oracle target DB instance<a name="Oracle.Procedural.Importing.DataPumpDBLink.Step1"></a>

To grant privileges to the user on the RDS for Oracle target DB instance, take the following steps:

1. Use SQL Plus or Oracle SQL Developer to connect to the RDS for Oracle DB instance into which you intend to import the data\. Connect as the Amazon RDS master user\. For information about connecting to the DB instance, see [Connecting to your Oracle DB instance](USER_ConnectToOracleInstance.md)\.

1. Create the required tablespaces before you import the data\. For more information, see [Creating and sizing tablespaces](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.CreatingTablespacesAndDatafiles)\.

1. If the user account into which the data is imported doesn't exist, create the user account and grant the necessary permissions and roles\. If you plan to import data into multiple user schemas, create each user account and grant the necessary privileges and roles to it\.

   For example, the following commands create a new user named *schema\_1* and grant the necessary permissions and roles to import the data into the schema for this user\.

   ```
   CREATE USER schema_1 IDENTIFIED BY my-password;
   GRANT CREATE SESSION, RESOURCE TO schema_1;
   ALTER USER schema_1 QUOTA 100M ON users;
   ```

   The preceding example grants the new user the `CREATE SESSION` privilege and the `RESOURCE` role\. Additional privileges and roles might be required depending on the database objects that you import\. 
**Note**  
Replace `schema_1` with the name of your schema in this step and in the following steps\.

### Step 2: Grant privileges to the user on the source database<a name="Oracle.Procedural.Importing.DataPumpDBLink.Step2"></a>

Use SQL\*Plus or Oracle SQL Developer to connect to the RDS for Oracle DB instance that contains the data to be imported\. If necessary, create a user account and grant the necessary permissions\. 

**Note**  
If the source database is an Amazon RDS instance, you can skip this step\. You use your Amazon RDS master user account to perform the export\.

The following commands create a new user and grant the necessary permissions\.

```
CREATE USER export_user IDENTIFIED BY my-password;
GRANT CREATE SESSION, CREATE TABLE, CREATE DATABASE LINK TO export_user;
ALTER USER export_user QUOTA 100M ON users;
GRANT READ, WRITE ON DIRECTORY data_pump_dir TO export_user;
GRANT SELECT_CATALOG_ROLE TO export_user;
GRANT EXECUTE ON DBMS_DATAPUMP TO export_user;
GRANT EXECUTE ON DBMS_FILE_TRANSFER TO export_user;
```

### Step 3: Create a dump file using DBMS\_DATAPUMP<a name="Oracle.Procedural.Importing.DataPumpDBLink.Step3"></a>

To create a dump file, do the following:

1. Use SQL\*Plus or Oracle SQL Developer to connect to the source Oracle instance with an administrative user or with the user you created in step 2\. If the source database is an Amazon RDS for Oracle DB instance, connect with the Amazon RDS master user\.

1. Create a dump file using the Oracle Data Pump utility\.

   The following script creates a dump file named *sample\.dmp* in the `DATA_PUMP_DIR` directory\. 

   ```
   DECLARE
     v_hdnl NUMBER;
   BEGIN
     v_hdnl := DBMS_DATAPUMP.OPEN( 
       operation => 'EXPORT' , 
       job_mode  => 'SCHEMA' , 
       job_name  => null
     );
     DBMS_DATAPUMP.ADD_FILE( 
       handle    => v_hdnl, 
       filename  => 'sample.dmp'    , 
       directory => 'DATA_PUMP_DIR' , 
       filetype  => dbms_datapump.ku$_file_type_dump_file
     );
     DBMS_DATAPUMP.ADD_FILE( 
       handle    => v_hdnl           , 
       filename  => 'sample_exp.log' , 
       directory => 'DATA_PUMP_DIR'  , 
       filetype  => dbms_datapump.ku$_file_type_log_file
     );
     DBMS_DATAPUMP.METADATA_FILTER(
       v_hdnl              ,
       'SCHEMA_EXPR'       ,
       'IN (''SCHEMA_1'')'
     );
     DBMS_DATAPUMP.METADATA_FILTER(
       v_hdnl,
       'EXCLUDE_NAME_EXPR',
       q'[IN (SELECT NAME FROM sys.OBJ$ 
              WHERE TYPE# IN (66,67,74,79,59,62,46) 
              AND OWNER# IN 
                (SELECT USER# FROM SYS.USER$ 
                 WHERE NAME IN ('RDSADMIN','SYS','SYSTEM','RDS_DATAGUARD','RDSSEC')
                )
             )
       ]',
       'PROCOBJ'
     );
     DBMS_DATAPUMP.START_JOB(v_hdnl);
   END;
   /
   ```
**Note**  
Data Pump jobs are started asynchronously\. For information about monitoring a Data Pump job, see [ Monitoring job status](https://docs.oracle.com/en/database/oracle/oracle-database/19/sutil/oracle-data-pump-overview.html#GUID-E365D74E-12CD-495C-BA23-5A55F679C7E7) in the Oracle documentation\. You can view the contents of the export log by using the `rdsadmin.rds_file_util.read_text_file` procedure\. For more information, see [Reading files in a DB instance directory](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.ReadingFiles)\.

### Step 4: Create a database link to the target DB instance<a name="Oracle.Procedural.Importing.DataPumpDBLink.Step4"></a>

Create a database link between your source DB instance and your target DB instance\. Your local Oracle instance must have network connectivity to the DB instance in order to create a database link and to transfer your export dump file\. 

Perform this step connected with the same user account as the previous step\.

If you are creating a database link between two DB instances inside the same VPC or peered VPCs, the two DB instances should have a valid route between them\. The security group of each DB instance must allow ingress to and egress from the other DB instance\. The security group inbound and outbound rules can refer to security groups from the same VPC or a peered VPC\. For more information, see [Adjusting database links for use with DB instances in a VPC](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.DBLinks)\. 

The following command creates a database link named `to_rds` that connects to the Amazon RDS master user at the target DB instance\. 

```
CREATE DATABASE LINK to_rds 
  CONNECT TO <master_user_account> IDENTIFIED BY <password>
  USING '(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<dns or ip address of remote db>)
         (PORT=<listener port>))(CONNECT_DATA=(SID=<remote SID>)))';
```

### Step 5: Copy the exported dump file to the target DB instance using DBMS\_FILE\_TRANSFER<a name="Oracle.Procedural.Importing.DataPumpDBLink.Step5"></a>

Use `DBMS_FILE_TRANSFER` to copy the dump file from the source database instance to the target DB instance\. The following script copies a dump file named sample\.dmp from the source instance to a target database link named *to\_rds* \(created in the previous step\)\. 

```
BEGIN
  DBMS_FILE_TRANSFER.PUT_FILE(
    source_directory_object       => 'DATA_PUMP_DIR',
    source_file_name              => 'sample.dmp',
    destination_directory_object  => 'DATA_PUMP_DIR',
    destination_file_name         => 'sample_copied.dmp', 
    destination_database          => 'to_rds' );
END;
/
```

### Step 6: Import the data file to the target DB instance using DBMS\_DATAPUMP<a name="Oracle.Procedural.Importing.DataPumpDBLink.Step6"></a>

Use Oracle Data Pump to import the schema in the DB instance\. Additional options such as METADATA\_REMAP might be required\. 

 Connect to the DB instance with the Amazon RDS master user account to perform the import\. 

```
DECLARE
  v_hdnl NUMBER;
BEGIN
  v_hdnl := DBMS_DATAPUMP.OPEN( 
    operation => 'IMPORT', 
    job_mode  => 'SCHEMA', 
    job_name  => null);
  DBMS_DATAPUMP.ADD_FILE( 
    handle    => v_hdnl, 
    filename  => 'sample_copied.dmp',
    directory => 'DATA_PUMP_DIR', 
    filetype  => dbms_datapump.ku$_file_type_dump_file );
  DBMS_DATAPUMP.ADD_FILE( 
    handle    => v_hdnl, 
    filename  => 'sample_imp.log', 
    directory => 'DATA_PUMP_DIR', 
    filetype  => dbms_datapump.ku$_file_type_log_file);
  DBMS_DATAPUMP.METADATA_FILTER(v_hdnl,'SCHEMA_EXPR','IN (''SCHEMA_1'')');
  DBMS_DATAPUMP.START_JOB(v_hdnl);
END;
/
```

**Note**  
Data Pump jobs are started asynchronously\. For information about monitoring a Data Pump job, see [ Monitoring job status](https://docs.oracle.com/en/database/oracle/oracle-database/19/sutil/oracle-data-pump-overview.html#GUID-E365D74E-12CD-495C-BA23-5A55F679C7E7) in the Oracle documentation\. You can view the contents of the import log by using the `rdsadmin.rds_file_util.read_text_file` procedure\. For more information, see [Reading files in a DB instance directory](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.ReadingFiles)\.

You can verify the data import by viewing the user's tables on the DB instance\. For example, the following query returns the number of tables for `schema_1`\. 

```
SELECT COUNT(*) FROM DBA_TABLES WHERE OWNER='SCHEMA_1'; 
```

### Step 7: Clean up<a name="Oracle.Procedural.Importing.DataPumpDBLink.Step7"></a>

After the data has been imported, you can delete the files that you don't want to keep\. You can list the files in `DATA_PUMP_DIR` using the following command\.

```
SELECT * FROM TABLE(rdsadmin.rds_file_util.listdir('DATA_PUMP_DIR')) ORDER BY MTIME;
```

To delete files in `DATA_PUMP_DIR` that you no longer require, use the following command\. 

```
EXEC UTL_FILE.FREMOVE('DATA_PUMP_DIR','<file name>');
```

For example, the following command deletes the file named `"sample_copied.dmp"`\. 

```
EXEC UTL_FILE.FREMOVE('DATA_PUMP_DIR','sample_copied.dmp'); 
```