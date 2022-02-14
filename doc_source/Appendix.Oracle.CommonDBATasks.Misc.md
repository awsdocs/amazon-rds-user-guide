# Performing miscellaneous tasks for Oracle DB instances<a name="Appendix.Oracle.CommonDBATasks.Misc"></a>

Following, you can find how to perform miscellaneous DBA tasks on your Amazon RDS DB instances running Oracle\. To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances, and restricts access to certain system procedures and tables that require advanced privileges\. 

**Topics**
+ [Creating and dropping directories in the main data storage space](#Appendix.Oracle.CommonDBATasks.NewDirectories)
+ [Listing files in a DB instance directory](#Appendix.Oracle.CommonDBATasks.ListDirectories)
+ [Reading files in a DB instance directory](#Appendix.Oracle.CommonDBATasks.ReadingFiles)
+ [Accessing Opatch files](#Appendix.Oracle.CommonDBATasks.accessing-opatch-files)
+ [Managing advisor tasks](#Appendix.Oracle.CommonDBATasks.managing-advisor-tasks)
+ [Enabling HugePages for an Oracle DB instance](#Oracle.Concepts.HugePages)
+ [Enabling extended data types](#Oracle.Concepts.ExtendedDataTypes)

## Creating and dropping directories in the main data storage space<a name="Appendix.Oracle.CommonDBATasks.NewDirectories"></a>

To create directories, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.create_directory`\. You can create up to 10,000 directories, all located in your main data storage space\. To drop directories, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.drop_directory`\.

The `create_directory` and `drop_directory` procedures have the following required parameter\. 


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_directory_name`  |  varchar2  |  —  |  Yes  |  The name of the directory\.  | 

The following example creates a new directory named `PRODUCT_DESCRIPTIONS`\. 

```
EXEC rdsadmin.rdsadmin_util.create_directory(p_directory_name => 'product_descriptions');
```

The data dictionary stores the directory name in uppercase\. You can list the directories by querying `DBA_DIRECTORIES`\. The system chooses the actual host pathname automatically\. The following example gets the directory path for the directory named `PRODUCT_DESCRIPTIONS`: 

```
SELECT DIRECTORY_PATH 
  FROM DBA_DIRECTORIES 
 WHERE DIRECTORY_NAME='PRODUCT_DESCRIPTIONS';
        
DIRECTORY_PATH
----------------------------------------
/rdsdbdata/userdirs/01
```

The master user name for the DB instance has read and write privileges in the new directory, and can grant access to other users\. `EXECUTE` privileges are not available for directories on a DB instance\. Directories are created in your main data storage space and will consume space and I/O bandwidth\. 

The following example drops the directory named `PRODUCT_DESCRIPTIONS`\. 

```
EXEC rdsadmin.rdsadmin_util.drop_directory(p_directory_name => 'product_descriptions');
```

**Note**  
You can also drop a directory by using the Oracle SQL command `DROP DIRECTORY`\. 

Dropping a directory doesn't remove its contents\. Because the `rdsadmin.rdsadmin_util.create_directory` procedure can reuse pathnames, files in dropped directories can appear in a newly created directory\. Before you drop a directory, we recommend that you use `UTL_FILE.FREMOVE` to remove files from the directory\. For more information, see [FREMOVE procedure](https://docs.oracle.com/database/121/ARPLS/u_file.htm#ARPLS70924) in the Oracle documentation\.

## Listing files in a DB instance directory<a name="Appendix.Oracle.CommonDBATasks.ListDirectories"></a>

To list the files in a directory, use the Amazon RDS procedure `rdsadmin.rds_file_util.listdir`\. The `listdir` procedure has the following parameters\. 


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_directory`  |  varchar2  |  —  |  Yes  |  The name of the directory to list\.  | 

The following example grants read/write privileges on the directory `PRODUCT_DESCRIPTIONS` to user `rdsadmin`, and then lists the files in this directory\. 

```
GRANT READ,WRITE ON DIRECTORY PRODUCT_DESCRIPTIONS TO rdsadmin;
SELECT * FROM TABLE(rdsadmin.rds_file_util.listdir(p_directory => 'PRODUCT_DESCRIPTIONS'));
```

## Reading files in a DB instance directory<a name="Appendix.Oracle.CommonDBATasks.ReadingFiles"></a>

To read a text file, use the Amazon RDS procedure `rdsadmin.rds_file_util.read_text_file`\. The `read_text_file` procedure has the following parameters\. 


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_directory`  |  varchar2  |  —  |  Yes  |  The name of the directory that contains the file\.  | 
|  `p_filename`  |  varchar2  |  —  |  Yes  |  The name of the file to read\.  | 

The following example creates the file `rice.txt` in the directory `PRODUCT_DESCRIPTIONS`\. 

```
declare
  fh sys.utl_file.file_type;
begin
  fh := utl_file.fopen(location=>'PRODUCT_DESCRIPTIONS', filename=>'rice.txt', open_mode=>'w');
  utl_file.put(file=>fh, buffer=>'AnyCompany brown rice, 15 lbs');
  utl_file.fclose(file=>fh);
end;
/
```

The following example reads the file `rice.txt` from the directory `PRODUCT_DESCRIPTIONS`\. 

```
SELECT * FROM TABLE
    (rdsadmin.rds_file_util.read_text_file(
        p_directory => 'PRODUCT_DESCRIPTIONS',
        p_filename  => 'rice.txt'));
```

## Accessing Opatch files<a name="Appendix.Oracle.CommonDBATasks.accessing-opatch-files"></a>

Opatch is an Oracle utility that enables the application and rollback of patches to Oracle software\. The Oracle mechanism for determining which patches have been applied to a database is the `opatch lsinventory` command\. To open service requests for Bring Your Own Licence \(BYOL\) customers, Oracle Support requests the `lsinventory` file and sometimes the `lsinventory_detail` file generated by Opatch\.

To deliver a managed service experience, Amazon RDS doesn't provide shell access to Opatch\. Instead, the `lsinventory-dbv.txt` in the BDUMP directory contains the patch information related to your current engine version\. When you perform a minor or major upgrade, Amazon RDS updates `lsinventory-dbv.txt` within an hour of applying the patch\. To verify the applied patches, read `lsinventory-dbv.txt`\. This action is similar to running the `opatch lsinventory` command\.

**Note**  
The examples in this section assume that the BDUMP directory is named `BDUMP`\. On a read replica, the BDUMP directory name is different\. To learn how to get the BDUMP name by querying `V$DATABASE.DB_UNIQUE_NAME` on a read replica, see [Listing files](USER_LogAccess.Concepts.Oracle.md#USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles.ViewingBackgroundDumpDest)\.

The inventory files use the Amazon RDS naming convention `lsinventory-dbv.txt` and `lsinventory_detail-dbv.txt`, where *dbv* is the full name of your DB version\. The `lsinventory-dbv.txt` file is available on all DB versions\. The corresponding `lsinventory_detail-dbv.txt` is available on the following DB versions:
+ 19\.0\.0\.0, ru\-2020\-01\.rur\-2020\-01\.r1 or later
+ 12\.2\.0\.1, ru\-2020\-01\.rur\-2020\-01\.r1 or later
+ 12\.1\.0\.2, v19 or later

For example, if your DB version is 19\.0\.0\.0\.ru\-2021\-07\.rur\-2021\-07\.r1, then your inventory files have the following names\.

```
lsinventory-19.0.0.0.ru-2021-07.rur-2021-07.r1.txt
lsinventory_detail-19.0.0.0.ru-2021-07.rur-2021-07.r1.txt
```

Ensure that you download the files that match the current version of your DB engine\.

### Console<a name="Appendix.Oracle.CommonDBATasks.accessing-opatch-files.console"></a>

**To download an inventory file using the console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the name of the DB instance that has the log file that you want to view\.

1. Choose the **Logs & events** tab\.

1. Scroll down to the **Logs** section\.

1. In the **Logs** section, search for `lsinventory`\.

1. Select the file that you want to access, and then choose **Download**\.

### SQL<a name="Appendix.Oracle.CommonDBATasks.accessing-opatch-files.sql"></a>

To read the `lsinventory-dbv.txt` in a SQL client, you can use a `SELECT` statement\. For this technique, use either of the following `rdsadmin` functions: `rdsadmin.rds_file_util.read_text_file` or `rdsadmin.tracefile_listing`\.

In the following sample query, replace *dbv* with your Oracle DB version\. For example, your DB version might be 19\.0\.0\.0\.ru\-2020\-04\.rur\-2020\-04\.r1\.

```
SELECT text
FROM   TABLE(rdsadmin.rds_file_util.read_text_file('BDUMP', 'lsinventory-dbv.txt'));
```

### PL/SQL<a name="Appendix.Oracle.CommonDBATasks.accessing-opatch-files.plsql"></a>

To read the `lsinventory-dbv.txt` in a SQL client, you can write a PL/SQL program\. This program uses `utl_file` to read the file, and `dbms_output` to print it\. These are Oracle\-supplied packages\. 

In the following sample program, replace *dbv* with your Oracle DB version\. For example, your DB version might be 19\.0\.0\.0\.ru\-2020\-04\.rur\-2020\-04\.r1\.

```
SET SERVEROUTPUT ON
DECLARE
  v_file              SYS.UTL_FILE.FILE_TYPE;
  v_line              VARCHAR2(1000);
  v_oracle_home_type  VARCHAR2(1000);
  c_directory         VARCHAR2(30) := 'BDUMP';
  c_output_file       VARCHAR2(30) := 'lsinventory-dbv.txt';
BEGIN
  v_file := SYS.UTL_FILE.FOPEN(c_directory, c_output_file, 'r');
  LOOP
    BEGIN
      SYS.UTL_FILE.GET_LINE(v_file, v_line,1000);
      DBMS_OUTPUT.PUT_LINE(v_line);
    EXCEPTION
      WHEN no_data_found THEN
        EXIT;
    END;
  END LOOP;
END;
/
```

Or query `rdsadmin.tracefile_listing`, and spool the output to a file\. The following example spools the output to `/tmp/tracefile.txt`\.

```
SPOOL /tmp/tracefile.txt
SELECT * 
FROM   rdsadmin.tracefile_listing 
WHERE  FILENAME LIKE 'lsinventory%';
SPOOL OFF;
```

## Managing advisor tasks<a name="Appendix.Oracle.CommonDBATasks.managing-advisor-tasks"></a>

Oracle Database includes a number of advisors\. Each advisor supports automated and manual tasks\. You can use procedures in the `rdsadmin.rdsadmin_util` package to manage some advisor tasks\.

The advisor task procedures are available in the following engine versions:
+ [Version 19\.0\.0\.0\.ru\-2021\-01\.rur\-2021\-01\.r1](Appendix.Oracle.RU-RUR.19.0.0.0.md#Appendix.Oracle.RU-RUR.19.0.0.0.ru-2021-01.rur-2021-01.r1) or higher 19c versions 
+ [Version 12\.2\.0\.1\.ru\-2021\-01\.rur\-2021\-01\.r1](Appendix.Oracle.RU-RUR.12.2.0.1.md#Appendix.Oracle.RU-RUR.12.2.0.1.ru-2021-01.rur-2021-01.r1) or higher 12\.2\.0\.1 versions 

**Topics**
+ [Setting parameters for advisor tasks](#Appendix.Oracle.CommonDBATasks.setting-task-parameters)
+ [Disabling AUTO\_STATS\_ADVISOR\_TASK](#Appendix.Oracle.CommonDBATasks.dropping-advisor-task)
+ [Re\-enabling AUTO\_STATS\_ADVISOR\_TASK](#Appendix.Oracle.CommonDBATasks.recreating-advisor-task)

### Setting parameters for advisor tasks<a name="Appendix.Oracle.CommonDBATasks.setting-task-parameters"></a>

To set parameters for some advisor tasks, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.advisor_task_set_parameter`\. The `advisor_task_set_parameter` procedure has the following parameters\.


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_task_name`  |  varchar2  |  —  |  Yes  |  The name of the advisor task whose parameters you want to change\. The following values are valid: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.Oracle.CommonDBATasks.Misc.html)  | 
|  `p_parameter`  |  varchar2  |  —  |  Yes  |  The name of the task parameter\. To find valid parameters for an advisor task, run the following query\. Substitute *p\_task\_name* with a valid value for `p_task_name`: <pre>COL PARAMETER_NAME FORMAT a30<br />COL PARAMETER_VALUE FORMAT a30<br />SELECT PARAMETER_NAME, PARAMETER_VALUE<br />FROM DBA_ADVISOR_PARAMETERS<br />WHERE TASK_NAME='p_task_name'<br />AND PARAMETER_VALUE != 'UNUSED'<br />ORDER BY PARAMETER_NAME;</pre>  | 
|  `p_value`  |  varchar2  |  —  |  Yes  |  The value for a task parameter\. To find valid values for task parameters, run the following query\. Substitute *p\_task\_name* with a valid value for `p_task_name`: <pre>COL PARAMETER_NAME FORMAT a30<br />COL PARAMETER_VALUE FORMAT a30<br />SELECT PARAMETER_NAME, PARAMETER_VALUE<br />FROM DBA_ADVISOR_PARAMETERS<br />WHERE TASK_NAME='p_task_name'<br />AND PARAMETER_VALUE != 'UNUSED'<br />ORDER BY PARAMETER_NAME;</pre>  | 

The following PL/SQL program sets `ACCEPT_PLANS` to `FALSE` for `SYS_AUTO_SPM_EVOLVE_TASK`\. The SQL Plan Management automated task verifies the plans and generates a report of its findings, but does not evolve the plans automatically\. You can use a report to identify new SQL plan baselines and accept them manually\.

```
BEGIN 
  rdsadmin.rdsadmin_util.advisor_task_set_parameter(
    p_task_name => 'SYS_AUTO_SPM_EVOLVE_TASK',
    p_parameter => 'ACCEPT_PLANS',
    p_value     => 'FALSE');
END;
```

The following PL/SQL program sets `EXECUTION_DAYS_TO_EXPIRE` to `10` for `AUTO_STATS_ADVISOR_TASK`\. The predefined task `AUTO_STATS_ADVISOR_TASK` runs automatically in the maintenance window once per day\. The example sets the retention period for the task execution to 10 days\. 

```
BEGIN 
  rdsadmin.rdsadmin_util.advisor_task_set_parameter(
    p_task_name => 'AUTO_STATS_ADVISOR_TASK',
    p_parameter => 'EXECUTION_DAYS_TO_EXPIRE',
    p_value     => '10');
END;
```

### Disabling AUTO\_STATS\_ADVISOR\_TASK<a name="Appendix.Oracle.CommonDBATasks.dropping-advisor-task"></a>

To disable `AUTO_STATS_ADVISOR_TASK`, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.advisor_task_drop`\. The `advisor_task_drop` procedure accepts the following parameter\.

**Note**  
This procedure is available in Oracle Database 12c Release 2 \(12\.2\.0\.1\) and later\.


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_task_name`  |  varchar2  |  —  |  Yes  |  The name of the advisor task to be disabled\. The only valid value is `AUTO_STATS_ADVISOR_TASK`\.  | 

The following command drops `AUTO_STATS_ADVISOR_TASK`\.

```
EXEC rdsadmin.rdsadmin_util.advisor_task_drop('AUTO_STATS_ADVISOR_TASK')
```

You can re\-enabling `AUTO_STATS_ADVISOR_TASK` using `rdsadmin.rdsadmin_util.dbms_stats_init`\.

### Re\-enabling AUTO\_STATS\_ADVISOR\_TASK<a name="Appendix.Oracle.CommonDBATasks.recreating-advisor-task"></a>

To re\-enable `AUTO_STATS_ADVISOR_TASK`, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.dbms_stats_init`\. The `dbms_stats_init` procedure takes no parameters\.

The following command re\-enables `AUTO_STATS_ADVISOR_TASK`\.

```
EXEC rdsadmin.rdsadmin_util.dbms_stats_init()
```

## Enabling HugePages for an Oracle DB instance<a name="Oracle.Concepts.HugePages"></a>

Amazon RDS for Oracle supports Linux kernel HugePages for increased database scalability\. HugePages results in smaller page tables and less CPU time spent on memory management, increasing the performance of large database instances\. For more information, see [Overview of HugePages](https://docs.oracle.com/database/121/UNXAR/appi_vlm.htm#UNXAR400) in the Oracle documentation\. 

You can use HugePages with the following versions and editions of Oracle Database: 
+ 19\.0\.0\.0, all editions
+ 12\.2\.0\.1, all editions
+ 12\.1\.0\.2, all editions

 The `use_large_pages` parameter controls whether HugePages are enabled for a DB instance\. The possible settings for this parameter are `ONLY`, `FALSE`, and `{DBInstanceClassHugePagesDefault}`\. The `use_large_pages` parameter is set to `{DBInstanceClassHugePagesDefault}` in the default DB parameter group for Oracle\. 

To control whether HugePages are enabled for a DB instance automatically, you can use the `DBInstanceClassHugePagesDefault` formula variable in parameter groups\. The value is determined as follows:
+ For the DB instance classes mentioned in the table following, `DBInstanceClassHugePagesDefault` always evaluates to `FALSE` by default, and `use_large_pages` evaluates to `FALSE`\. You can enable HugePages manually for these DB instance classes if the DB instance class has at least 14 GiB of memory\.
+ For DB instance classes not mentioned in the table following, if the DB instance class has less than 14 GiB of memory, `DBInstanceClassHugePagesDefault` always evaluates to `FALSE`\. Also, `use_large_pages` evaluates to `FALSE`\.
+ For DB instance classes not mentioned in the table following, if the instance class has at least 14 GiB of memory and less than 100 GiB of memory, `DBInstanceClassHugePagesDefault` evaluates to `TRUE` by default\. Also, `use_large_pages` evaluates to `ONLY`\. You can disable HugePages manually by setting `use_large_pages` to `FALSE`\.
+ For DB instance classes not mentioned in the table following, if the instance class has at least 100 GiB of memory, `DBInstanceClassHugePagesDefault` always evaluates to `TRUE`\. Also, `use_large_pages` evaluates to `ONLY` and HugePages can't be disabled\.

HugePages are not enabled by default for the following DB instance classes\. 


****  

| DB instance class family | DB instance classes with HugePages not enabled by default | 
| --- | --- | 
|  db\.m5  |  db\.m5\.large  | 
|  db\.m4  |  db\.m4\.large, db\.m4\.xlarge, db\.m4\.2xlarge, db\.m4\.4xlarge, db\.m4\.10xlarge  | 
|  db\.t3  |  db\.t3\.micro, db\.t3\.small, db\.t3\.medium, db\.t3\.large  | 

For more information about DB instance classes, see [Hardware specifications for DB instance classes](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Summary)\. 

To enable HugePages for new or existing DB instances manually, set the `use_large_pages` parameter to `ONLY`\. You can't use HugePages with Oracle Automatic Memory Management \(AMM\)\. If you set the parameter `use_large_pages` to `ONLY`, then you must also set both `memory_target` and `memory_max_target` to `0`\. For more information about setting DB parameters for your DB instance, see [Working with DB parameter groups](USER_WorkingWithParamGroups.md)\. 

You can also set the `sga_target`, `sga_max_size`, and `pga_aggregate_target` parameters\. When you set system global area \(SGA\) and program global area \(PGA\) memory parameters, add the values together\. Subtract this total from your available instance memory \(`DBInstanceClassMemory`\) to determine the free memory beyond the HugePages allocation\. You must leave free memory of at least 2 GiB, or 10 percent of the total available instance memory, whichever is smaller\. 

After you configure your parameters, you must reboot your DB instance for the changes to take effect\. For more information, see [Rebooting a DB instance](USER_RebootInstance.md)\. 

**Note**  
The Oracle DB instance defers changes to SGA\-related initialization parameters until you reboot the instance without failover\. In the Amazon RDS console, choose **Reboot** but *do not* choose **Reboot with failover**\. In the AWS CLI, call the `reboot-db-instance` command with the `--no-force-failover` parameter\. The DB instance does not process the SGA\-related parameters during failover or during other maintenance operations that cause the instance to restart\.

The following is a sample parameter configuration for HugePages that enables HugePages manually\. You should set the values to meet your needs\. 

```
1. memory_target            = 0
2. memory_max_target        = 0
3. pga_aggregate_target     = {DBInstanceClassMemory*1/8}
4. sga_target               = {DBInstanceClassMemory*3/4}
5. sga_max_size             = {DBInstanceClassMemory*3/4}
6. use_large_pages          = ONLY
```

Assume the following parameters values are set in a parameter group\.

```
1. memory_target            = IF({DBInstanceClassHugePagesDefault}, 0, {DBInstanceClassMemory*3/4})
2. memory_max_target        = IF({DBInstanceClassHugePagesDefault}, 0, {DBInstanceClassMemory*3/4})
3. pga_aggregate_target     = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*1/8}, 0)
4. sga_target               = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*3/4}, 0)
5. sga_max_size             = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*3/4}, 0)
6. use_large_pages          = {DBInstanceClassHugePagesDefault}
```

The parameter group is used by a db\.r4 DB instance class with less than 100 GiB of memory\. With these parameter settings and `use_large_pages` set to `{DBInstanceClassHugePagesDefault}`, HugePages are enabled on the db\.r4 instance\.

Consider another example with following parameters values set in a parameter group\.

```
1. memory_target           = IF({DBInstanceClassHugePagesDefault}, 0, {DBInstanceClassMemory*3/4})
2. memory_max_target       = IF({DBInstanceClassHugePagesDefault}, 0, {DBInstanceClassMemory*3/4})
3. pga_aggregate_target    = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*1/8}, 0)
4. sga_target              = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*3/4}, 0)
5. sga_max_size            = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*3/4}, 0)
6. use_large_pages         = FALSE
```

The parameter group is used by a db\.r4 DB instance class and a db\.r5 DB instance class, both with less than 100 GiB of memory\. With these parameter settings, HugePages are disabled on the db\.r4 and db\.r5 instance\.

**Note**  
If this parameter group is used by a db\.r4 DB instance class or db\.r5 DB instance class with at least 100 GiB of memory, the `FALSE` setting for `use_large_pages` is overridden and set to `ONLY`\. In this case, a customer notification regarding the override is sent\.

After HugePages are active on your DB instance, you can view HugePages information by enabling enhanced monitoring\. For more information, see [Monitoring OS metrics with Enhanced Monitoring](USER_Monitoring.OS.md)\. 

## Enabling extended data types<a name="Oracle.Concepts.ExtendedDataTypes"></a>

Amazon RDS Oracle Database 12c supports extended data types\. With extended data types, the maximum size is 32,767 bytes for the VARCHAR2, NVARCHAR2, and RAW data types\. To use extended data types, set the `MAX_STRING_SIZE` parameter to `EXTENDED`\. For more information, see [Extended data types](https://docs.oracle.com/database/121/SQLRF/sql_elements001.htm#SQLRF55623) in the Oracle documentation\. 

If you don't want to use extended data types, keep the `MAX_STRING_SIZE` parameter set to `STANDARD` \(the default\)\. When this parameter is set to `STANDARD`, the size limits are 4,000 bytes for the VARCHAR2 and NVARCHAR2 data types, and 2,000 bytes for the RAW data type\.

You can enable extended data types on a new or existing DB instance\. For new DB instances, DB instance creation time is typically longer when you enable extended data types\. For existing DB instances, the DB instance is unavailable during the conversion process\.

The following are considerations for a DB instance with extended data types enabled:
+ When you enable extended data types for a DB instance, you can't change the DB instance back to use the standard size for data types\. After a DB instance is converted to use extended data types, if you set the `MAX_STRING_SIZE` parameter back to `STANDARD` it results in the `incompatible-parameters` status\.
+ When you restore a DB instance that uses extended data types, you must specify a parameter group with the `MAX_STRING_SIZE` parameter set to `EXTENDED`\. During restore, if you specify the default parameter group or any other parameter group with `MAX_STRING_SIZE` set to `STANDARD` it results in the `incompatible-parameters` status\.
+ We recommend that you don't enable extended data types for Oracle DB instances running on the t2\.micro DB instance class\.

When the DB instance status is `incompatible-parameters` because of the `MAX_STRING_SIZE` setting, the DB instance remains unavailable until you set the `MAX_STRING_SIZE` parameter to `EXTENDED` and reboot the DB instance\.

### Enabling extended data types for a new DB instance<a name="Oracle.Concepts.ExtendedDataTypes.CreateDBInstance"></a>

**To enable extended data types for a new DB instance**

1. Set the `MAX_STRING_SIZE` parameter to `EXTENDED` in a parameter group\.

   To set the parameter, you can either create a new parameter group or modify an existing parameter group\.

   For more information, see [Working with DB parameter groups](USER_WorkingWithParamGroups.md)\.

1. Create a new Amazon RDS Oracle DB instance, and associate the parameter group with `MAX_STRING_SIZE` set to `EXTENDED` with the DB instance\.

   For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.

### Enabling extended data types for an existing DB instance<a name="Oracle.Concepts.ExtendedDataTypes.ModifyDBInstance"></a>

When you modify a DB instance to enable extended data types, the data in the database is converted to use the extended sizes\. The DB instance is unavailable during the conversion\. The amount of time it takes to convert the data depends on the DB instance class used by the DB instance and the size of the database\.

**Note**  
After you enable extended data types, you can't perform a point\-in\-time restore to a time during the conversion\. You can restore to the time immediately before the conversion or after the conversion\.

**To enable extended data types for an existing DB instance**

1. Take a snapshot of the database\.

   If there are invalid objects in the database, Amazon RDS tries to recompile them\. The conversion to extended data types can fail if Amazon RDS can't recompile an invalid object\. The snapshot enables you to restore the database if there is a problem with the conversion\. Always check for invalid objects before conversion and fix or drop those invalid objects\. For production databases, we recommend testing the conversion process on a copy of your DB instance first\.

   For more information, see [Creating a DB snapshot](USER_CreateSnapshot.md)\.

1. Set the `MAX_STRING_SIZE` parameter to `EXTENDED` in a parameter group\.

   To set the parameter, you can either create a new parameter group or modify an existing parameter group\.

   For more information, see [Working with DB parameter groups](USER_WorkingWithParamGroups.md)\.

1. Modify the DB instance to associate it with the parameter group with `MAX_STRING_SIZE` set to `EXTENDED`\.

   For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

1. Reboot the DB instance for the parameter change to take effect\.

   For more information, see [Rebooting a DB instance](USER_RebootInstance.md)\.