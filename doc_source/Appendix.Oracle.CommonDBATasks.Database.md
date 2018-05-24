# Common DBA Database Tasks for Oracle DB Instances<a name="Appendix.Oracle.CommonDBATasks.Database"></a>

This section describes how you can perform common DBA tasks related to databases on your Amazon RDS DB instances running Oracle\. To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances, and restricts access to certain system procedures and tables that require advanced privileges\. 

## Changing the Global Name of a Database<a name="Appendix.Oracle.CommonDBATasks.RenamingGlobalName"></a>

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.rename_global_name` to change the global name of a database\. The `rename_global_name` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_new_global_name` | varchar2 | — | required | The new global name for the database\. | 

The database must be open for the name change to occur\. For more information about changing the global name of a database, see [ALTER DATABASE](http://docs.oracle.com/cd/E11882_01/server.112/e41084/statements_1004.htm#SQLRF52547) in the Oracle documentation\. 

The following example changes the global name of a database to `new_global_name`\.

```
1. exec rdsadmin.rdsadmin_util.rename_global_name(p_new_global_name => 'new_global_name');
```

## Creating and Sizing Tablespaces<a name="Appendix.Oracle.CommonDBATasks.CreatingTablespacesAndDatafiles"></a>

Amazon RDS only supports Oracle Managed Files \(OMF\) for data files, log files and control files\. When you create data files and log files, you can't specify the physical file names\. 

By default, tablespaces are created with auto\-extend enabled, and no maximum size\. Because of these default settings, tablespaces can grow to consume all allocated storage\. We recommend that you specify an appropriate maximum size on permanent and temporary tablespaces, and that you carefully monitor space usage\. 

The following example creates a tablespace named `users2` with a starting size of 1 gigabyte and a maximum size of 10 gigabytes: 

```
1. create tablespace users2 datafile size 1G autoextend on maxsize 10G;
```

The following example creates temporary tablespace named `temp01`:

```
1. create temporary tablespace temp01;
```

The Oracle `ALTER DATABASE` system privilege is not available on Amazon RDS\. We recommend that you don't use smallfile tablespaces, because you can only perform some operations, such as resizing existing datafiles, by using the `ALTER DATABASE` statement\. 

You can resize a bigfile tablespace by using `ALTER TABLESPACE`\. You can specify the size in kilobytes \(K\), megabytes \(M\), gigabytes \(G\), or terabytes \(T\)\. 

The following example resizes a bigfile tablespace named `users2` to 200 MB: 

```
1. alter tablespace users2 resize 200M;
```

The following example adds an additional datafile to a smallfile tablespace named users2: 

```
1. alter tablespace users2 add datafile size 100000M autoextend on next 250m maxsize UNLIMITED;
```

## Setting the Default Tablespace<a name="Appendix.Oracle.CommonDBATasks.SettingDefaultTablespace"></a>

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.alter_default_tablespace` to set the default tablespace\. The `alter_default_tablespace` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `tablespace_name` | varchar | — | required | The name of the default tablespace\. | 

The following example sets the default tablespace to *users2*: 

```
1. exec rdsadmin.rdsadmin_util.alter_default_tablespace(tablespace_name => 'users2');
```

## Setting the Default Temporary Tablespace<a name="Appendix.Oracle.CommonDBATasks.SettingDefTempTablespace"></a>

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.alter_default_temp_tablespace` to set the default temporary tablespace\. The `alter_default_temp_tablespace` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `tablespace_name` | varchar | — | required | The name of the default temporary tablespace\. | 

The following example sets the default temporary tablespace to *temp01*: 

```
1. exec rdsadmin.rdsadmin_util.alter_default_temp_tablespace(tablespace_name => 'temp01');
```

## Checkpointing the Database<a name="Appendix.Oracle.CommonDBATasks.CheckpointingDatabase"></a>

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.checkpoint` to checkpoint the database\. The `checkpoint` procedure has no parameters\. 

The following example checkpoints the database: 

```
1. exec rdsadmin.rdsadmin_util.checkpoint;
```

## Setting Distributed Recovery<a name="Appendix.Oracle.CommonDBATasks.SettingDistributedRecovery"></a>

You can use the Amazon RDS procedures `rdsadmin.rdsadmin_util.enable_distr_recovery` and `disable_distr_recovery` to set distributed recovery\. The procedures have no parameters\. 

The following example enables distributed recovery: 

```
1. exec rdsadmin.rdsadmin_util.enable_distr_recovery;
```

The following example disables distributed recovery: 

```
1. exec rdsadmin.rdsadmin_util.disable_distr_recovery;
```

## Setting the Database Time Zone<a name="Appendix.Oracle.CommonDBATasks.TimeZoneSupport"></a>

There are two different ways that you can set the time zone of your Amazon RDS Oracle database: 
+ You can use the `Timezone` option\. 

  The `Timezone` option changes the time zone at the host level and impacts all date columns and values such as `SYSDATE`\. For more information about the `Timezone` option, see [Oracle Time Zone](Appendix.Oracle.Options.Timezone.md)\. 
+ You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.alter_db_time_zone`\. 

  The `alter_db_time_zone` procedure changes the time zone for only certain data types, and doesn't change `SYSDATE`\. There are additional restrictions on setting the time zone listed in the [Oracle documentation](http://docs.oracle.com/cd/B19306_01/server.102/b14225/ch4datetime.htm#i1006705)\. 

The `alter_db_time_zone` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_new_tz` | varchar2 | — | required |  The new time zone as a named region or an absolute offset from Coordinated Universal Time \(UTC\)\. Valid offsets range from \-12:00 to \+14:00\.   | 

The following example changes the time zone to UTC plus 3 hours: 

```
1. exec rdsadmin.rdsadmin_util.alter_db_time_zone(p_new_tz => '+3:00');
```

The following example changes the time zone to the time zone of the Africa/Algiers region: 

```
1. exec rdsadmin.rdsadmin_util.alter_db_time_zone(p_new_tz => 'Africa/Algiers');
```

After you alter the time zone by using the `alter_db_time_zone` procedure, you must reboot the DB instance for the change to take effect\. For more information, see [Rebooting a DB Instance](USER_RebootInstance.md)\. 

## Working with Oracle External Tables<a name="Appendix.Oracle.CommonDBATasks.External_Tables"></a>

*Oracle external tables *are tables with data that is not in the database\. Instead, the data is in external files that the database can access\. By using external tables, you can access data without loading it into the database\. For more information about external tables, see [Managing External Tables](http://docs.oracle.com/database/121/ADMIN/tables.htm#ADMIN01507) in the Oracle documentation\. 

With Amazon RDS, you can store external table files in directory objects\. You can create a directory object, or you can use one that is predefined in the Oracle database, such as the DATA\_PUMP\_DIR directory\. For information about creating directory objects, see [Creating New Directories in the Main Data Storage Space](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.NewDirectories)\. You can query the ALL\_DIRECTORIES view to list the directory objects for your Amazon RDS Oracle DB instance\.

**Note**  
Directory objects point to the main data storage space \(Amazon EBS volume\) used by your instance\. The space used—along with data files, redo logs, audit, trace, and other files—counts against allocated storage\.

You can move an external data file from one Oracle database to another by using the [ DBMS\_FILE\_TRANSFER](https://docs.oracle.com/database/121/ARPLS/d_ftran.htm#ARPLS095) package or the [UTL\_FILE](https://docs.oracle.com/database/121/ARPLS/u_file.htm#ARPLS069) package\. The external data file is moved from a directory on the source database to the specified directory on the destination database\. For information about using DBMS\_FILE\_TRANSFER, see [Oracle Data Pump](Oracle.Procedural.Importing.md#Oracle.Procedural.Importing.DataPump)\.

After you move the external data file, you can create an external table with it\. The following example creates an external table that uses the `emp_xt_file1.txt` file in the USER\_DIR1 directory:

```
CREATE TABLE emp_xt (
  emp_id      NUMBER,
  first_name  VARCHAR2(50),
  last_name   VARCHAR2(50),
  user_name   VARCHAR2(20)
)
ORGANIZATION EXTERNAL (
  TYPE ORACLE_LOADER
  DEFAULT DIRECTORY USER_DIR1
  ACCESS PARAMETERS (
    RECORDS DELIMITED BY NEWLINE
    FIELDS TERMINATED BY ','
    MISSING FIELD VALUES ARE NULL
    (emp_id,first_name,last_name,user_name)
  )
  LOCATION ('emp_xt_file1.txt')
)
PARALLEL
REJECT LIMIT UNLIMITED;
```

Suppose that you want to move data that is in an Amazon RDS Oracle DB instance into an external data file\. In this case, you can populate the external data file by creating an external table and selecting the data from the table in the database\. For example, the following SQL statement creates the `orders_xt` external table by querying the `orders` table in the database\.

```
CREATE TABLE orders_xt
  ORGANIZATION EXTERNAL
   (
     TYPE ORACLE_DATAPUMP
     DEFAULT DIRECTORY DATA_PUMP_DIR
     LOCATION ('orders_xt.dmp')
   )
   AS SELECT * FROM orders;
```

In this example, the data is populated in the `orders_xt.dmp` file in the DATA\_PUMP\_DIR directory\.

## Working with Automatic Workload Repository \(AWR\)<a name="Appendix.Oracle.CommonDBATasks.AWR"></a>

If you use Oracle Database Enterprise Edition and want to use Automatic Workload Repository \(AWR\), you can enable AWR by changing the `CONTROL_MANAGEMENT_PACK_ACCESS` parameter\. 

Oracle AWR includes several report generation scripts, such as awrrpt\.sql, that are installed on the host server\. You do not have direct access to the host, but you can copy the scripts from another installation of Oracle Database\. 

## Adjusting Database Links for Use with DB Instances in a VPC<a name="Appendix.Oracle.CommonDBATasks.DBLinks"></a>

To use Oracle database links with Amazon RDS DB instances inside the same VPC or peered VPCs, the two DB instances should have a valid route between them\. Verify the valid route between the DB instances by using your VPC routing tables and network access control list \(ACL\)\. 

The security group of each DB instance must allow ingress to and egress from the other DB instance\. The inbound and outbound rules can refer to security groups from the same VPC or a peered VPC\. For more information, see [Updating Your Security Groups to Reference Peered VPC Security Groups](http://docs.aws.amazon.com/AmazonVPC/latest/PeeringGuide/working-with-vpc-peering.html#vpc-peering-security-groups)\. 

If you have configured a custom DNS server using the DHCP Option Sets in your VPC, your custom DNS server must be able to resolve the name of the database link target\. For more information, see [Setting Up a Custom DNS Server](Appendix.Oracle.CommonDBATasks.System.md#Appendix.Oracle.CommonDBATasks.CustomDNS)\. 

For more information about using database links with Oracle Data Pump, see [Oracle Data Pump](Oracle.Procedural.Importing.md#Oracle.Procedural.Importing.DataPump)\. 

## Setting the Default Edition for a DB Instance<a name="Appendix.Oracle.CommonDBATasks.DefaultEdition"></a>

You can redefine database objects in a private environment called an edition\. You can use edition\-based redefinition to upgrade an application's database objects with minimal downtime\. 

You can set the default edition of an Amazon RDS Oracle DB instance using the Amazon RDS procedure `rdsadmin.rdsadmin_util.alter_default_edition`\. 

The following example sets the default edition for the Amazon RDS Oracle DB instance to `RELEASE_V1`\. 

```
1. exec rdsadmin.rdsadmin_util.alter_default_edition('RELEASE_V1');
```

The following example sets the default edition for the Amazon RDS Oracle DB instance back to the Oracle default\. 

```
1. exec rdsadmin.rdsadmin_util.alter_default_edition('ORA$BASE');
```

For more information about Oracle edition\-based redefinition, see [About Editions and Edition\-Based Redefinition](https://docs.oracle.com/database/121/ADMIN/general.htm#ADMIN13167) in the Oracle documentation\.

## Validating DB Instance Files<a name="Appendix.Oracle.CommonDBATasks.ValidateDBFiles"></a>

You can use the Amazon RDS package `rdsadmin.rdsadmin_rman_util` to validate Amazon RDS Oracle DB instance files, such as data files, server parameter files \(SPFILEs\), and control files\.

**Note**  
The `rdsadmin.rdsadmin_rman_util` package provides capabilities that are available with Oracle Recovery Manager \(RMAN\) validation\. While Amazon RDS does not use RMAN for backups, you can use the package to execute RMAN validation commands against the database, control file, SPFILE, tablespaces, or data files\. For more information about RMAN validation, see [ Validating Database Files and Backups](https://docs.oracle.com/database/121/BRADV/rcmvalid.htm#BRADV90063) and [ VALIDATE](https://docs.oracle.com/database/121/RCMRF/rcmsynta2025.htm#RCMRF162) in the Oracle documentation\.

### Validating a DB Instance<a name="Appendix.Oracle.CommonDBATasks.ValidateDB"></a>

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.validate_database` to validate all of the relevant files used by an Amazon RDS Oracle DB instance\. 


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
| `p_validation_type` | varchar2 | `'PHYSICAL'`, `'PHYSICAL+LOGICAL'` | `'PHYSICAL'` | Optional | The level of corruption detection\. Specify `'PHYSICAL'` to check for physical corruption\. An example of physical corruption is a block with a mismatch in the header and footer\. Specify `'PHYSICAL+LOGICAL'` to check for logical inconsistencies in addition to physical corruption\. An example of logical corruption is a corrupt block\.  | 
| `p_parallel` | number | A valid integer between `1` and `254` for Oracle Database Enterprise Edition \(EE\) `1` for other Oracle Database editions | `1` | Optional | Number of channels\. | 
| `p_section_size_mb` | number | A valid integer | `NULL` | Optional | The section size in megabytes \(MB\)\. Validates in parallel by dividing each file into the specified section size\. When `NULL`, the parameter is ignored\. | 
| `p_rman_to_dbms_output` | boolean | `TRUE`, `FALSE` | `FALSE` | Optional | When `TRUE`, the RMAN output is sent to the `DBMS_OUTPUT` package in addition to a file in the `BDUMP` directory\. When using SQL\*Plus, execute `SET SERVEROUTPUT ON` to see the output\. When `FALSE`, the RMAN output is only sent to a file in the `BDUMP` directory\.  | 

The following example validates the DB instance using the default values for the parameters:

```
1. exec rdsadmin.rdsadmin_rman_util.validate_database;
```

The following example validates the DB instance using the specified values for the parameters:

```
1. BEGIN
2.     rdsadmin.rdsadmin_rman_util.validate_database(
3.         p_validation_type     => 'PHYSICAL+LOGICAL', 
4.         p_parallel            => 4,  
5.         p_section_size_mb     => 10,
6.         p_rman_to_dbms_output => FALSE);
7. END;
8. /
```

When the `p_rman_to_dbms_output` parameter is set to `FALSE`, the RMAN output is written to a file in the `BDUMP` directory\.

To view the files in the `BDUMP` directory, run the following `SELECT` statement:

```
1. SELECT * FROM table(rdsadmin.rds_file_util.listdir('BDUMP')) order by mtime;
```

To view the contents of a file in the `BDUMP` directory, run the following `SELECT` statement:

```
1. SELECT text FROM table(rdsadmin.rds_file_util.read_text_file('BDUMP','rds-rman-validate-nnn.txt'));
```

Replace the file name with the name of the file you want to view\.

### Validating a Tablespace<a name="Appendix.Oracle.CommonDBATasks.ValidateTablespace"></a>

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.validate_tablespace` to validate the files associated with a tablespace\. 


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
| `p_tablespace_name` | varchar2 | A valid tablespace name | — | Required | The name of the tablespace\. | 
| `p_validation_type` | varchar2 | `'PHYSICAL'`, `'PHYSICAL+LOGICAL'` | `'PHYSICAL'` | Optional | The level of corruption detection\. Specify `'PHYSICAL'` to check for physical corruption\. An example of physical corruption is a block with a mismatch in the header and footer\. Specify `'PHYSICAL+LOGICAL'` to check for logical inconsistencies in addition to physical corruption\. An example of logical corruption is a corrupt block\.  | 
| `p_parallel` | number | A valid integer between `1` and `254` for Oracle Database Enterprise Edition \(EE\) `1` for other Oracle Database editions | `1` | Optional | Number of channels\. | 
| `p_section_size_mb` | number | A valid integer | `NULL` | Optional | The section size in megabytes \(MB\)\. Validates in parallel by dividing each file into the specified section size\. When `NULL`, the parameter is ignored\. | 
| `p_rman_to_dbms_output` | boolean | `TRUE`, `FALSE` | `FALSE` | Optional | When `TRUE`, the RMAN output is sent to the `DBMS_OUTPUT` package in addition to a file in the `BDUMP` directory\. When using SQL\*Plus, execute `SET SERVEROUTPUT ON` to see the output\. When `FALSE`, the RMAN output is only sent to a file in the `BDUMP` directory\.  | 

### Validating a Control File<a name="Appendix.Oracle.CommonDBATasks.ValidateControlFile"></a>

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.validate_current_controlfile` to validate only the control file used by an Amazon RDS Oracle DB instance\. 


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
| `p_validation_type` | varchar2 | `'PHYSICAL'`, `'PHYSICAL+LOGICAL'` | `'PHYSICAL'` | Optional | The level of corruption detection\. Specify `'PHYSICAL'` to check for physical corruption\. An example of physical corruption is a block with a mismatch in the header and footer\. Specify `'PHYSICAL+LOGICAL'` to check for logical inconsistencies in addition to physical corruption\. An example of logical corruption is a corrupt block\.  | 
| `p_rman_to_dbms_output` | boolean | `TRUE`, `FALSE` | `FALSE` | Optional | When `TRUE`, the RMAN output is sent to the `DBMS_OUTPUT` package in addition to a file in the `BDUMP` directory\. When using SQL\*Plus, execute `SET SERVEROUTPUT ON` to see the output\. When `FALSE`, the RMAN output is only sent to a file in the `BDUMP` directory\.  | 

### Validating an SPFILE<a name="Appendix.Oracle.CommonDBATasks.ValidateSpfile"></a>

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.validate_spfile` to validate only the server parameter file \(SPFILE\) used by an Amazon RDS Oracle DB instance\. 


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
| `p_validation_type` | varchar2 | `'PHYSICAL'`, `'PHYSICAL+LOGICAL'` | `'PHYSICAL'` | Optional | The level of corruption detection\. Specify `'PHYSICAL'` to check for physical corruption\. An example of physical corruption is a block with a mismatch in the header and footer\. Specify `'PHYSICAL+LOGICAL'` to check for logical inconsistencies in addition to physical corruption\. An example of logical corruption is a corrupt block\.  | 
| `p_rman_to_dbms_output` | boolean | `TRUE`, `FALSE` | `FALSE` | Optional | When `TRUE`, the RMAN output is sent to the `DBMS_OUTPUT` package in addition to a file in the `BDUMP` directory\. When using SQL\*Plus, execute `SET SERVEROUTPUT ON` to see the output\. When `FALSE`, the RMAN output is only sent to a file in the `BDUMP` directory\.  | 

### Validating a Data File<a name="Appendix.Oracle.CommonDBATasks.ValidateDataFile"></a>

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.validate_datafile` to validate a data file\. 


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
| `p_datafile` | varchar2 | A valid tablespace name | — | Required | The name of the data file\. | 
| `p_from_block` | number | A valid integer | `NULL` | Optional | The number of the block where the validation starts within the data file\. When `NULL`, `1` is used\. | 
| `p_to_block` | number | A valid integer | `NULL` | Optional | The number of the block where the validation ends within the data file\. When `NULL`, the max block in the data file is used\. | 
| `p_validation_type` | varchar2 | `'PHYSICAL'`, `'PHYSICAL+LOGICAL'` | `'PHYSICAL'` | Optional | The level of corruption detection\. Specify `'PHYSICAL'` to check for physical corruption\. An example of physical corruption is a block with a mismatch in the header and footer\. Specify `'PHYSICAL+LOGICAL'` to check for logical inconsistencies in addition to physical corruption\. An example of logical corruption is a corrupt block\.  | 
| `p_parallel` | number | A valid integer between `1` and `254` for Oracle Database Enterprise Edition \(EE\) `1` for other Oracle Database editions | `1` | Optional | Number of channels\. | 
| `p_section_size_mb` | number | A valid integer | `NULL` | Optional | The section size in megabytes \(MB\)\. Validates in parallel by dividing each file into the specified section size\. When `NULL`, the parameter is ignored\. | 
| `p_rman_to_dbms_output` | boolean | `TRUE`, `FALSE` | `FALSE` | Optional | When `TRUE`, the RMAN output is sent to the `DBMS_OUTPUT` package in addition to a file in the `BDUMP` directory\. When using SQL\*Plus, execute `SET SERVEROUTPUT ON` to see the output\. When `FALSE`, the RMAN output is only sent to a file in the `BDUMP` directory\.  | 

## Related Topics<a name="Appendix.Oracle.CommonDBATasks.Database.Related"></a>
+ [Common DBA System Tasks for Oracle DB Instances](Appendix.Oracle.CommonDBATasks.System.md)
+ [Common DBA Log Tasks for Oracle DB Instances](Appendix.Oracle.CommonDBATasks.Log.md)
+ [Common DBA Miscellaneous Tasks for Oracle DB Instances](Appendix.Oracle.CommonDBATasks.Misc.md)