# Performing miscellaneous tasks for Oracle DB instances<a name="Appendix.Oracle.CommonDBATasks.Misc"></a>

Following, you can find how to perform miscellaneous DBA tasks on your Amazon RDS DB instances running Oracle\. To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances, and restricts access to certain system procedures and tables that require advanced privileges\. 

**Topics**
+ [Creating and dropping directories in the main data storage space](#Appendix.Oracle.CommonDBATasks.NewDirectories)
+ [Listing files in a DB instance directory](#Appendix.Oracle.CommonDBATasks.ListDirectories)
+ [Reading files in a DB instance directory](#Appendix.Oracle.CommonDBATasks.ReadingFiles)
+ [Accessing Opatch files](#Appendix.Oracle.CommonDBATasks.accessing-opatch-files)
+ [Managing advisor tasks](#Appendix.Oracle.CommonDBATasks.managing-advisor-tasks)

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
+ Oracle Database 21c \(21\.0\.0\)
+ Version 19\.0\.0\.0\.ru\-2021\-01\.rur\-2021\-01\.r1 and higher Oracle Database 19c versions 

  For more information, see [Version 19\.0\.0\.0\.ru\-2021\-01\.rur\-2021\-01\.r1](https://docs.aws.amazon.com/AmazonRDS/latest/OracleReleaseNotes/oracle-version-19-0.html#oracle-version-RU-RUR.19.0.0.0.ru-2021-01.rur-2021-01.r1) in the *Amazon RDS for Oracle Release Notes*\.
+ Version 12\.2\.0\.1\.ru\-2021\-01\.rur\-2021\-01\.r1 and higher Oracle Database 12c \(Release 2\) 12\.2\.0\.1 versions 

  For more information, see [Version 12\.2\.0\.1\.ru\-2021\-01\.rur\-2021\-01\.r1](https://docs.aws.amazon.com/AmazonRDS/latest/OracleReleaseNotes/oracle-version-12-2.html#oracle-version-RU-RUR.12.2.0.1.ru-2021-01.rur-2021-01.r1) in the *Amazon RDS for Oracle Release Notes*\.

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