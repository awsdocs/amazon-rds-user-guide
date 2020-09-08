# Other Common DBA Tasks for Oracle DB Instances<a name="Appendix.Oracle.CommonDBATasks.Misc"></a>

Following, you can find how to perform miscellaneous DBA tasks on your Amazon RDS DB instances running Oracle\. To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances, and restricts access to certain system procedures and tables that require advanced privileges\. 

**Topics**
+ [Creating and Dropping Directories in the Main Data Storage Space](#Appendix.Oracle.CommonDBATasks.NewDirectories)
+ [Listing Files in a DB Instance Directory](#Appendix.Oracle.CommonDBATasks.ListDirectories)
+ [Reading Files in a DB Instance Directory](#Appendix.Oracle.CommonDBATasks.ReadingFiles)
+ [Accessing Opatch Files](#Appendix.Oracle.CommonDBATasks.accessing-opatch-files)

## Creating and Dropping Directories in the Main Data Storage Space<a name="Appendix.Oracle.CommonDBATasks.NewDirectories"></a>

To create directories, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.create_directory`\. You can create up to 10,000 directories, all located in your main data storage space\. To drop directories, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.drop_directory`\.

The `create_directory` and `drop_directory` procedures have the following required parameter\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_directory_name`  |  varchar2  |  —  |  Yes  |  The name of the directory\.  | 

The following example creates a new directory named `PRODUCT_DESCRIPTIONS`\. 

```
exec rdsadmin.rdsadmin_util.create_directory(p_directory_name => 'product_descriptions');
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
exec rdsadmin.rdsadmin_util.drop_directory(p_directory_name => 'product_descriptions');
```

**Note**  
You can also drop a directory by using the Oracle SQL command `DROP DIRECTORY`\. 

Dropping a directory doesn't remove its contents\. Because the `rdsadmin.rdsadmin_util.create_directory` procedure can reuse pathnames, files in dropped directories can appear in a newly created directory\. Before you drop a directory, we recommend that you use `UTL_FILE.FREMOVE` to remove files from the directory\. For more information, see [FREMOVE Procedure](https://docs.oracle.com/database/121/ARPLS/u_file.htm#ARPLS70924) in the Oracle documentation\.

## Listing Files in a DB Instance Directory<a name="Appendix.Oracle.CommonDBATasks.ListDirectories"></a>

To list the files in a directory, use the Amazon RDS procedure `rdsadmin.rds_file_util.listdir`\. The `listdir` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_directory`  |  varchar2  |  —  |  Yes  |  The name of the directory to list\.  | 

The following example lists the files in the directory named `PRODUCT_DESCRIPTIONS`\. 

```
SELECT * FROM TABLE(rdsadmin.rds_file_util.listdir(p_directory => 'PRODUCT_DESCRIPTIONS'));
```

## Reading Files in a DB Instance Directory<a name="Appendix.Oracle.CommonDBATasks.ReadingFiles"></a>

To read a text file, use the Amazon RDS procedure `rdsadmin.rds_file_util.read_text_file`\. The `read_text_file` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
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

## Accessing Opatch Files<a name="Appendix.Oracle.CommonDBATasks.accessing-opatch-files"></a>

Opatch is an Oracle utility that enables the application and rollback of patches to Oracle software\. The Oracle mechanism for determining which patches have been applied to a database is the `opatch lsinventory` command\. To open service requests for Bring Your Own Licence \(BYOL\) customers, Oracle Support requests the `lsinventory` file and sometimes the `lsinventory_detail` file generated by Opatch\.

To deliver a managed service experience, Amazon RDS doesn't provide shell access to Opatch\. Instead, the Oracle DB instance automatically creates the inventory files every hour in the BDUMP directory\. You have read and write access on this directory\. If you don't see your files in BDUMP, or the files are out of date, wait an hour and then try again\.

The inventory files use the Amazon RDS naming convention `lsinventory-dbv.txt` and `lsinventory_detail-dbv.txt`, where *dbv* is the full name of your DB version\. The `lsinventory-dbv.txt` file is available on all DB versions\. The corresponding detail file is available on the following DB versions:
+ 19\.0\.0\.0, ru\-2020\-01\.rur\-2020\-01\.r1 or later
+ 18\.0\.0\.0, ru\-2020\-01\.rur\-2020\-01\.r1 or later
+ 12\.2\.0\.1, ru\-2020\-01\.rur\-2020\-01\.r1 or later
+ 12\.1\.0\.2, v19 or later
+ 11\.2\.0\.4, v23 or later

For example, if your DB version is 19\.0\.0\.0\.ru\-2020\-04\.rur\-2020\-04\.r1, then your inventory files have the following names\.

```
lsinventory-19.0.0.0.ru-2020-04.rur-2020-04.r1.txt
lsinventory_detail-19.0.0.0.ru-2020-04.rur-2020-04.r1.txt
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
FROM   TABLE(rdsadmin.rds_file_util.read_text_file('BDUMP',' lsinventory-dbv.txt'));
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
spool /tmp/tracefile.txt
SELECT * 
FROM   rdsadmin.tracefile_listing 
WHERE  FILENAME LIKE 'lsinventory%';
spool off;
```