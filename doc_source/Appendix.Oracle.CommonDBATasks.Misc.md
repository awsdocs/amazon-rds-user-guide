# Common DBA Miscellaneous Tasks for Oracle DB Instances<a name="Appendix.Oracle.CommonDBATasks.Misc"></a>

This section describes how you can perform miscellaneous DBA tasks on your Amazon RDS DB instances running Oracle\. To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances, and restricts access to certain system procedures and tables that require advanced privileges\. 

**Topics**
+ [Creating New Directories in the Main Data Storage Space](#Appendix.Oracle.CommonDBATasks.NewDirectories)
+ [Listing Files in a DB Instance Directory](#Appendix.Oracle.CommonDBATasks.ListDirectories)
+ [Reading Files in a DB Instance Directory](#Appendix.Oracle.CommonDBATasks.ReadingFiles)

## Creating New Directories in the Main Data Storage Space<a name="Appendix.Oracle.CommonDBATasks.NewDirectories"></a>

To create directories, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.create_directory`\. You can create up to 10,000 directories, all located in your main data storage space\. 

The `create_directory` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_directory_name`  |  varchar2  |  —  |  Yes  |  The name of the new directory\.  | 

The following example creates a new directory named **product\_descriptions**\. 

```
exec rdsadmin.rdsadmin_util.create_directory(p_directory_name => 'product_descriptions');
```

You can list the directories by querying `DBA_DIRECTORIES`\. The system chooses the actual host pathname automatically\. The following example gets the directory path for the directory named `product_descriptions`: 

```
select DIRECTORY_PATH 
  from DBA_DIRECTORIES 
 where DIRECTORY_NAME='product_descriptions';
        
DIRECTORY_PATH
----------------------------------------
/rdsdbdata/userdirs/01
```

The master user name for the DB instance has read and write privileges in the new directory, and can grant access to other users\. Execute privileges are not available for directories on a DB instance\. Directories are created in your main data storage space and will consume space and I/O bandwidth\. 

You can drop a directory that you created by using the Oracle `drop directory` command\. Dropping a directory doesn't remove its contents\. Because the `create_directory()` method can reuse pathnames, files in dropped directories can appear in a newly created directory\. Before you drop a directory, you should use `UTL_FILE.FREMOVE` to remove files from the directory\. For more information, see [FREMOVE Procedure](https://docs.oracle.com/database/121/ARPLS/u_file.htm#ARPLS70924) in the Oracle documentation\. 

## Listing Files in a DB Instance Directory<a name="Appendix.Oracle.CommonDBATasks.ListDirectories"></a>

To list the files in a directory, use the Amazon RDS procedure `rdsadmin.rds_file_util.listdir`\. The `listdir` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_directory`  |  varchar2  |  —  |  Yes  |  The name of the directory to list\.  | 

The following example lists the files in the directory named `product_descriptions`\. 

```
select * from table
    (rdsadmin.rds_file_util.listdir(p_directory => 'product_descriptions'));
```

## Reading Files in a DB Instance Directory<a name="Appendix.Oracle.CommonDBATasks.ReadingFiles"></a>

To read a text file, use the Amazon RDS procedure `rdsadmin.rds_file_util.read_text_file`\. The `read_text_file` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_directory`  |  varchar2  |  —  |  Yes  |  The name of the directory that contains the file\.  | 
|  `p_filename`  |  varchar2  |  —  |  Yes  |  The name of the file to read\.  | 

The following example reads the file `rice.txt` from the directory `product_descriptions`\. 

```
select * from table
    (rdsadmin.rds_file_util.read_text_file(
        p_directory => 'product_descriptions',
        p_filename  => 'rice.txt'));
```